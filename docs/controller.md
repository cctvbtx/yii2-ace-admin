关于控制说明
===========

基础控制 `jinxing\admin\controllers\Controller` 
继承自 [yii\web\Controller](http://www.yiichina.com/doc/api/2.0/yii-web-controller), 
所有父类的方法，都可以使用

## 受保护属性，子类需要根据实际情况重写

|属性名称 | 类型 | 默认值 | 说明
|:----------|:---------|:---------|:-----------
|`$modelClass`| `string`   | `\jinxing\admin\models\Admin`  | [数据使用的 model](http://www.yiichina.com/doc/api/2.0/yii-db-activerecord)  
|`$pk`        | `string`   | `id`       | `model` 查询使用的主键 `key`
|`$sort`      | `string`   | `id`       | 默认排序使用的字段名称
|`$strategy`  | `string`   | `DataTables` or `JqGrid`| 使用DataTables 显示数据还是JqGrid 使用数据，根据模板视图使用的 js 修改
|`$uploadFromClass`|`string`| `jinxing\admin\models\forms\UploadForm`| 上传使用表单验证类(UploadForm 中需要定义上传文件字段和验证类型)
|`$strUploadPath`|`string` | `./uploads/`| 上传文件保存文件地址

## 请求公共的方法

方法名称           | 说明          | 相关方法
|:----------------|:------------         |:----------------------------
|`actionIndex()`    | 显示视图页面      |
|`actionSearch()`   | 为视图文件提供数据 |[where() Provide query conditions](#where-public-method)、[getQuery() Provide query object](#getquerywhere-protected-method)、[afterSearch() Data processing after query](#aftersearcharray-protected-method)
|`actionCreate()`   | 创建数据         |
|`actionUpdate()`   | 修改数据         |
|`actionDelete()`   | 删除数据         |
|`actionDeleteAll()`| 删除多条数据      |
|`actionEditable()` | 行内编辑         |
|`actionUpload()`   | 上传文件         | [ afterUpload() File upload processing](#afteruploadobject-strfilepath-strfield-protected-method)
|`actionExport()`   | 导出数据         | [where() Provide query conditions](#where-public-method)、[getQuery() Provide query object](#getquerywhere-protected-method)、[getExportHandleParams() Provide data export processing parameters ](#getexporthandleparams-protected-method)

## 公共方法

|方法名称   | 说明        |
|:---------|:-----------|
|`getPk()` | 获取主键名称|


如果是更复杂的查询，例如链接表查询，则需要覆盖 [getQuery()](#getquerywhere-protected-method) 方法.

## getQuery($where) protected method
> return [yii\db\Query](http://www.yiichina.com/doc/api/2.0/yii-db-query) or [yii\db\ActiveRecord](http://www.yiichina.com/doc/api/2.0/yii-db-activerecord) 

<table>
    <tr>
        <td colspan="3">protected function getQuery() </td>
    </tr>
    <tr>
        <td> $where </td>
        <td> array </td>
        <td> 根据前段请求数据生成的查询条件信息 </td>
    </tr>
</table>

>**如果是比较复杂的查询，可以复写这个方法(比如联表查询)**

```php 
    protected function getQuery($where)
    {
        return (new Query())->from('user')->leftJoin('actrive', 'user.id=active.user_id')->where($where);
    }

```

## afterSearch(&$array) protected method

用来对 [getQuery ()](#getquerywhere-protected-method) 方法查询出的分页数据做进一步处理(查询使用 model 查询，其实也可以在afterFind() 方法里面处理) :

```php
    protected function afterSearch(&$array) 
    {
        foreach ($array as &$value) {
            $value['created_at']  = date('Y-m-d H:i:s', $value['created_at']);
            $value['status_name'] = $value['status'] == 10 ? 'open' : 'close'; 
        }
        
        unset($value);
    }
```

## findOne($data = []) protected method

> return [yii\db\ActiveRecord](http://www.yiichina.com/doc/api/2.0/yii-db-activerecord)

通过请求参数查询到对象，没有会设置错误，返回false

## afterUpload($object, &$strFilePath, $strField) protected method

> return boolean

上传文件之后的处理，处理成功需要返回 ture

## getExportHandleParams() protected method
> return an array

对导出的数据做格式化处理(因为显示数据时在视图里面做的格式化处理，如果导出数据也要做格式化处理，需要定义这个方法):

```php
    protected function getExportHandleParams()
    {
        return [
            // 使用匿名函数处理，$value 为查询结果中 created_at 字段的值
            'created_at' => function ($value) {
                return date('Y-m-d H:i:s', $value);
            },
            'status' => function ($value) {
                return $value == 10 ? 'open' : 'close';
            }
        ];
    }
```

[←  关于 module 配置数据](./module.md) | [ 关于 meTables 配置数据 →](./metables.md)