---
title: 以 MySQL 為例解釋外鍵（Foreign Key）
date: 2020-05-09
comments: true
tags: 
- Foreign Key
categories: 
- MySQL
---

# 以 MySQL 為例解釋外鍵（Foreign Key）

外鍵（Foreign Key）是指向其他表的主鍵（Primary Key）的欄位，用於確定兩張表的關聯性及資料完整性，以避免部分資料匹配不上的問題。

* 如果想要使用外鍵，儲存引擎必須是 InnoDB
* 外鍵只能指向被設定為主鍵的欄位
* 外鍵與指向的主鍵資料型別必須相同

接下來會以 MySQL 示範 Foreign Key 的常見使用情境，以及操作時會碰到的問題。

## 實作

我們會有兩張資料表：users 及 orders。

users 負責管理會員資料，orders 則管理訂單資料。

這邊 users 與 orders 的關係為：先有 users（會員），才有 orders（訂單）。一個會員有多個訂單（反之則不可能），所以 users 為父表，orders 為子表，外鍵建立在 orders。

### 建立資料表

> users
```sql
CREATE TABLE users(
    userID INT(50) PRIMARY KEY,
    userName VARCHAR(30)
);
```

> orders
```sql
CREATE TABLE orders(
    orderID INT(50) PRIMARY KEY,
    userID INT(50),
    product VARCHAR(100),
    price INT(11),
    FOREIGN KEY(userID) REFERENCES users(userID)
);
```

### 新增資料

接下來我們各別在 users 及 orders 插入資料。

通常會員與訂單的關係都是先有會員、才有訂單，不會有反過來的情境。綁定外鍵的使用方式也是相同，因為外鍵放在 orders，所以 users 的資料必須先被建立才行。

換句話來說，不能先有訂單才有會員。

為了實驗，我們先在 orders 插入一筆資料看看：

```sql
INSERT INTO orders VALUES(1, 1, 'phone', 20000);
```

執行時報錯：

```
Error Code: 1216. Cannot add or update a child row: a foreign key constraint fails
```

第二個 1 是 userID，因為另一張表 users 找不到 userID 為 1 的資料，所以此筆插入會失敗。

綁定外鍵的效果就在於此，MySQL 會先確認父表的主鍵欄位有符合的資料，才可以插入資料。

按照標準流程先插入 users 資料，再插入 orders，就能正常執行了。

```sql
INSERT INTO users VALUES(1, 'Blueberry');
INSERT INTO orders VALUES(1, 1, 'phone', 20000);
```

> users

userID（主鍵 PK）|userName|
------|--------|
1|Blueberry|

> orders

orderID|userID（外鍵 FK）|product|price|
-------|------|-------|-----|
1|1|phone|20000|

### 刪除資料

如果想刪除 orders 資料，是沒有問題的，一個會員要取消訂單是很正常的操作。

但如果今天我們要刪除 users 的資料，並把這個 users 所屬的 orders 都一起刪除呢？

直接刪除 users？

```sql
DELETE FROM users WHERE userID = 1;
```

這樣操作會報錯：

```
Error Code: 1217. Cannot delete or update a parent row: a foreign key constraint fails
```

前面說過，只要綁定了外鍵，MySQL 就會拿父表（users）和子表（orders）進行比對。

所以如果要這樣操作，必須先把 users 所屬的 orders 都先刪除才行：

```sql
DELETE FROM orders WHERE userID = 1;
DELETE FROM users WHERE userID = 1;
```

但是不用這麼麻煩，我們只要在資料表建立時設定 `ON DELETE` 的模式就可以了。

#### ON DELECT 的四種模式

* RESTRICT（約束）：預設模式。當在父表刪除資料時，會比對子表是否有對應的資料，如果有則不允許刪除。
* CASCADE（級聯）：當在父表刪除資料時，會對比子表是否有對應的資料，如果有則會一起刪除。
* SET NULL：當在父表刪除資料時，會對比子表是否有對應的資料，如果有則設置子表的外鍵欄位為 `NULL`，子表資料不會被刪除。（欄位需允許 NULL）
* NO ACTION：與 RESTRICT 相同。

因此要達到前面的目的，可以在設定外鍵時使用 `ON DELETE CASCADE`。

```sql
CREATE TABLE orders(
    orderID INT(50) PRIMARY KEY,
    userID INT(50),
    product VARCHAR(100),
    price INT(11),
    FOREIGN KEY(userID) REFERENCES users(userID) ON DELETE CASCADE
);
```

設定為 CASCADE（級聯）就可以直接刪除 users 的資料，連同 orders 的資料也會一起不見。

### 編輯資料

編輯和刪除一樣，在預設的情況下無法直接改變綁定外鍵的欄位。根據不同需求，我們同樣可以在資料表建立時更改 `ON UPDATE` 的模式。

#### ON UPDATE 的四種模式

* RESTRICT（約束）：預設模式。父表修改主鍵欄位時，會比對子表是否有對應的資料，如果有則不允許修改。
* CASCADE（級聯）：當在父表修改主鍵欄位時，會對比子表是否有對應的資料，如果有則會一起修改。
* SET NULL：當在父表修改主鍵欄位時，會對比子表是否有對應的資料，如果有則設置子表的外鍵欄位為 `NULL`。（欄位需允許 NULL）
* NO ACTION：與 RESTRICT 相同。

```sql
CREATE TABLE orders(
    orderID INT(50) PRIMARY KEY,
    userID INT(50),
    product VARCHAR(100),
    price INT(11),
    FOREIGN KEY(userID) REFERENCES users(userID) ON DELETE CASCADE ON UPDATE CASCADE
);
```

> ON UPDATE 可以和 ON DELETE 一起設定。