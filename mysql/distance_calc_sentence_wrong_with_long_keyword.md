### 使用 mysql 的坐标计算时 long 错误
####   mysql 错误描述：`  SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'long * pi()) / 180 - (105 * pi()) / 180) / 2), 2))))) AS distance FROM **** at line 1`
####  计算的语句
```sql
(round(6367000 * 2 * asin(sqrt(pow(sin(((lat * pi()) / 180 - ({$latitude} * pi()) / 180) / 2), 2) + cos(({$latitude} * pi()) / 180) * cos((lat * pi()) / 180) * pow(sin(((         long          * pi()) / 180 - ({$longitude} * pi()) / 180) / 2), 2))))) AS distance
```
#### 错误的原因： `LONG` 作为 mysql 的关键词 ，而计算距离的语句中  `long`  字段与  `LONG` 关键词 引起冲突


#### 解决方案： 在  `long`字段 的左右 用反单引号  `  ，将 `long` 申明 为字段
```text
(round(6367000 * 2 * asin(sqrt(pow(sin(((lat * pi()) / 180 - ({$latitude} * pi()) / 180) / 2), 2) + cos(({$latitude} * pi()) / 180) * cos((lat * pi()) / 180) * pow(sin(((         `long`          * pi()) / 180 - ({$longitude} * pi()) / 180) / 2), 2))))) AS distance

```
