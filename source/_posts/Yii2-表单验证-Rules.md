---
title: Yii2 表单验证 Rules
date: 2019-05-23 16:36:28
categories:
    - PHP
tags:
    - PHP
---

## 基础规则
``` php
public function rules()
{
    return [
        ['字段名', 'required', 'message' => '提示信息', 'on' => '场景名'], // 必填验证
        ['字段名', 'email', 'message' => '提示信息', 'on' => '场景名'], // 邮箱格式验证
        ['字段名', 'url', 'message' => '提示信息', 'on' => '场景名'], // 网址格式验证
        ['字段名', 'match', 'pattern' => '正则表达式'], // 正则验证
        ['字段名', 'captcha'], // Yii 验证码验证
        ['字段名', 'safe'], // 安全不验证
        ['repassword', 'compare', 'compareAttribue' => 'password'], // 跟某个属性相等
        ['字段名', 'string', 'length' => [6, 18]], // 字符串长度验证，必须在6至18长度以内的
        ['字段名', 'unique'], // 值在本字段中的唯一性验证
        ['字段名', 'integer', 'max' => '上限', 'min' => '下限'], // 整数验证
        ['字段名', 'number', 'max' => '上限', 'min' => '下限'], // 数字验证
        ['字段名', 'double'], // 双精度浮点数验证
        ['字段名', 'in', 'range' => [1, 2, 3]], // 范围验证，必须在1,2,3以内的
        ['字段名', 'filter', 'filter' => 'trim'], // 过滤,删除字段两边的空格
        ['字段名', 'exist', 'targetClass' => '模型名'], // 字段名必须在模型名中存在
        ['字段名', 'file', 'extension' => 'jpg,png', 'maxSize' => 1024 * 1024 * 1024], // 允许上传以jpg，png为后缀，文件最大1024*1024*1024 Byte的文件
        ['字段名', 'validateType'], // 自定义函数验证
    ];
}
```

**备注：**
on 参数指定验证规则的场景，用逗号分开不同的场景，如果这个选项未设置，验证规则将应用到任何场景。
例：'on' => 'search'，['mobile', 'required', 'message' => '手机号不得为空', 'on'=>'search']，意思是在搜索场景中手机号必填。

## 自定义规则
``` php
// 校验类型
// rule：['type', 'validateType']

/**
 * @param string $attribute，当前正在被验证的属性
 * @param mixed $params，在规则中给定的名为“params”的参数值
 */
public function validateType($attribute, $params)
{
    if (!empty($this->attribute)) {
        return true;
    } else {
        $this->addError($attribute, '类型不存在');
        return false;
    }
}
```

## 规则校验
``` php
$model = new Model();
if ($model->load(Yii::$app->request->post(), '') && $model->validate()) {
    return true;
else {
    return $model->errors;
}
```
> getErrors()：
返回所有属性或单个属性的错误
即返回所有验证中发生的错误
>
> getFirstError()：
返回指定属性的第一个错误
>
> getFirstErrors()：
返回所有错误属性的第一条


## 引用
- https://www.kancloud.cn/manual/yii2-guide/69725
- 网络资料
