---
title: Yii 数据库操作
date: 2019-05-13 21:22:08
categories:
    - PHP
tags:
    - PHP
---

转自团队 wiki，适当调整格式。

[Yii2官方文档](https://www.yiichina.com/doc/guide/2.0/db-query-builder)，这里补充常用的数据操作说明，提高代码可读性。

## 处理类
生成查询实例

``` php
use yii\db\Query

$query = (new Query());
```

## select
字段名：

``` php
# 文本（函数需要用双引号“”）
->select('d.id')
->select("IFNULL(i.sum_count,d.sum_count) as sum_count")
```

``` php
# 数组（文本组成数组）
->select([
    'd.id',
    'd.channel_id',
    'd.batch_number', // 单个字段单引号即可
    "IFNULL(i.sum_count,d.sum_count) as sum_count", // 用函数的要用双引号
    "IFNULL(i.total_amount,d.total_amount) as total_amount",
    'd.begin_period',
    'd.end_period',
    'i.success_count',
    'i.fail_count',
    'd.created_on',
    'd.modified_on',
    'c.name channel_name',
])
```

## from
__->from(表名[别名])__

``` php
->from($this->getTableName() . ' d')
```

## join
__leftjoin(表名[别名], ON)__

``` php
# ON 必须为文本，复杂的用双引号
->leftJoin(ChannelRepository::instance()->getTableName() . ' c', 'c.id = d.channel_id')
->innerJoin($this->tableName . ' b', 'b.id = d.deduction_batch_id')
```

## where
__->where(mix)__

``` php
# 文本
->where('oa.project_id = :project_id') // 这种方式需要 ->params([':project_id' => '111']) 配合，通过 PDO 预处理，避免 SQL 注入。
->where('oa.project_id =' . $project_id) // 这种方式需要对文本的引号进行处理，比较麻烦，而且容易被注入。
```

``` php
# 数组(建议)
# 数组的组合很自由，且可以满足任何 where 条件
->where([
    'oa.project_id' => $queryParams['project_id'],
    'oa.channel_id' => $queryParams['channel_id'],
    'oa.is_deleted' => 0,
    'r.is_deleted' => 0,
    'r.bank_deduction' => null, // r.bank_deduction is null
    'r.item_id' => $item_ids, // 可以是=也可以是 in ('', ...)
])
```

## andWhere
这个方法主要用在多维数组的时候，可以跟其他where自由组合

``` php
# 不等于的情况
->andWhere([
    'and',
    ['>=', 'r.period', $queryParams['begin_period']],
    ['<=', 'r.period', $queryParams['end_period']]
])

# not in 子查询
->andWhere(['not in', 'r.id', $sub1])

# 子查询
$sub1 = (new Query())
    ->select(['receivable_id'])
    ->from(WorkflowDerateBusinessRepositories::instance()->getTableName() . ' wdb')
    ->innerJoin(WorkflowBusinessEntity::tableName() . ' wb', 'wb.business_id = wdb.business_id')
    ->innerJoin(WorkflowInstanceEntity::tableName() . ' wi', 'wi.id = wb.workflow_instance_id')
    ->where(['wi.status' => WorkflowInstanceEntity::STATUS_PENDING]);
```

## filterWhere
同 where ,但是会过滤空值。
>当一个值为 null、空数组、空字符串或者一个只包含空格的字符串时，那么它将被判定为空值。
不用写一堆 if (!empty()) { ... ->where ... }
**冗余代码示例，说明没有真正理解这个方法**

``` php
public function condition($params)
{
    $filterWhere = ['and', ['=', 'b.is_deleted', 0]];

    if (!empty($params['project_id'])) {
        $filterWhere[] = ['=', 'b.project_id', $params['project_id']];
    }
    if (!empty($params['room_id'])) {
        $filterWhere[] = ['=', 'b.room_id', $params['room_id']];
    }
    if (!empty($params['mobile'])) {
        $filterWhere[] = ['=', 'b.mobile', $params['mobile']];
    }
    if (!empty($params['user_id'])) {
        $filterWhere[] = ['=', 'b.user_id', $params['user_id']];
    }
    if (!empty($params['mid'])) {
        $filterWhere[] = ['=', 'b.mid', $params['mid']];
    }
    if (isset($params['current_binded'])) {
        $filterWhere[] = ['=', 'b.current_binded', $params['current_binded']];
    }

    if(isset($params['tenant_code'])){
        $filterWhere[] = ['=', 'b.tenant_code', $params['tenant_code']];
    }

  if (!empty($params['parent_project_id'])) {
      $filterWhere[] = ['=', 'b.parent_project_id', $params['parent_project_id']];
  }
    return $filterWhere;
}
```

## andFilterWhere
- 同 andWhere，用来组合
- 会过滤空值

## orderBy

``` php
# 数组
->orderBy([
    'id' => SORT_ASC,
    'name' => SORT_DESC,
])

# 文本
->orderBy('id ASC, name DESC')

# 组合
->orderBy('id ASC')
->addOrderBy('name DESC')
```

## groupBy
``` php
# 数组
->groupBy(['id', 'status'])

# 文本
->groupBy('id, status')
```

## having
同 where，可以理解为 group by 后的 where

## offset
limit 起点

## limit
limit 多少条记录

## createCommand
- 生成命令
- 传db连接

``` php
->createCommand($this->getDbHandler())
```

## queryAll
- 查询所有记录，返回二维数组

## update
``` php
$this->getDbHandler()
    ->createCommand()
    ->update(ReceivableRepository::instance()->getTableName(), ['bank_deduction' => 0], ['id' => $sub])
    ->execute();
```
