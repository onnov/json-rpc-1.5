# json-rpc-1.5
#### Alternative specification JSON Remote Procedure Call (RPC). JSON RPC Version 1.5 alt

## Request
```json
{
  "version": "1.5 alt",
  "method": "sys.test",
  "data": [10, 20, 30],
  "sign": "xxxxxxxxxx",
  "id": "999999999999"
}
```
### Description of parameters
* `version` - Protocol version number.
* `method` - Called method name.
* `data` - Data transferred to the method.
* `sign` - Signature or Token.
* `id` - The ID of the match between the request and the response. If the ID is missing or null, the request becomes a notification and does not require a response.

### Aliases for parameters
* `version` - `v`
* `method` - `m`
* `data` - `d`
* `sign` - `s`
* `id` - `i`

#### Alias example
```json
{
  "v": "1.5 alt",
  "m": "sys.test",
  "d": [10, 20, 30],
  "s": "xxxxxxxxxx",
  "i": "999999999999"
}
```

### Required parameter and its type

* `method` - any valid json value

##### example string
```
"method": "echo"
or
"method": "sys.echo"
```
##### example array
```
"method": ["sys", "echo"]
```
##### example json
```
"method": {"class": "sys", "method": "echo"}
```

#### Optional parameters and their types
* `version` - Any valid json value, but a better string.
* `data` - Any valid json value.
* `sign` - Any valid json value, but a better string.
* `id` - Any valid json value, but a better string

## Response

#### Successful
```json
{
  "version": "1.5 alt",
  "result": "success",
  "data": [100, 200, 300],
  "sign": "xxxxxxxxxx",
  "id": "999999999999"
}
```
#### or possible response on error
```json
{
  "version": "1.5 alt",
  "result": "error",
  "data": {
    "code": 123,
    "message": "An error occurred parsing the request object.",
    "info": {
      "meg": "Bad array",
      "at": 42,
      "text": "{\"id\":1,\"method\":\"sum\",\"params\":[1,2,3,4,5}"
    }
  },
  "sign": "xxxxxxxxxx",
  "id": "999999999999"
}
```
### Description of parameters
* `version` - Protocol version number.
* `result` - Description of the query result.
* `data` - Data received in response.
* `sign` - Signature or Token.
* `id` - The ID is the same as in the request.

### Aliases for parameters
* `version` - `v`
* `result` - `r`
* `data` - `d`
* `sign` - `s`
* `id` - `i`

#### Alias example
```json
{
  "v": "1.5 alt",
  "r": "success",
  "d": [100, 200, 300],
  "s": "xxxxxxxxxx",
  "i": "999999999999"
}
```

### Required parameter and its type

* `result` - any valid json value, but a better string.
##### example string
```
"result": "success"
or
"result": "ok"
or
"result": true
or
"result": "array"
or
"result": "json"
or
"result": null - it's not a error
or
"result": false
or
"result": "error"
```

#### Optional parameters and their types
* `version` - Any valid json value, but a better string.
* `data` - Any valid json value.
* `sign` - Any valid json value, but a better string.
* `id` - Any valid json value, but a better string


## Another request option
#### very short

В виде массива:

#### Request
* `version` - index `0`
* `method` - index `1`
* `data` - index `2`
* `sign` - index `3`
* `id` - index `4`

```json
[
  "1.5 alt",
  "sys.test",
  [10, 20, 30],
  "xxxxxxxxxx",
  "999999999999"
]
```

#### Response
* `version` - index `0`
* `result` - index `1`
* `data` - index `2`
* `sign` - index `3`
* `id` - index `4`

```json
[
  "1.5 alt",
  "success",
  [100, 200, 300],
  "xxxxxxxxxx",
  "999999999999"
]
```
Но в такой записи больше минусов, чем плюсов. Все параметры становятся обязательными. И их положение в массиве должно быть всегда одинаковым.


## Multi call example

## Request
```json
{
  "version": "1.5 alt",
  "method": "rpc.multicall",
  "data": [
    {
      "method": "sys.test",
      "data": [10, 20, 30],
      "id": "10"
    },
    {
      "method": "sys.echo",
      "data": "TEST",
      "id": "11"
    },
    {
      "method": "sys.sum",
      "data": [10, 20, 30],
      "id": "12"
    },
    {
      "method": "zig.zag",
      "data": {"a": 1, "b": 2},
      "id": "13"
    }
  ],
  "sign": "xxxxxxxxxx",
  "id": "999999999999"
}
```

## Response
```json
{
  "version": "1.5 alt",
  "result": "success",
  "data": [
    {
      "result": "success",
      "data": [100, 200, 300],
      "id": "10"
    },
    {
      "result": "success",
      "data": "TEST",
      "id": "11"
    },
    {
      "result": "success",
      "data": 60,
      "id": "12"
    },
    {
      "result": "error",
      "data": {
        "code": "???",
        "message": "Method not found"
      },
      "id": "13"
    }
  ],
  "sign": "xxxxxxxxxx",
  "id": "999999999999"
}
```
## 
## Why do we need the "sign" parameter?
Данный параметр не нужен, если авторизация и идентификация узлов обмена данными происходит за рамками RPC. Или обмен данными происходит по защищеному каналу или в защищенной сети.

Но, возможно, будет удобно посылать сразу "подписанный" запрос, не тратя время на авторизацию.

Или "клиент" может получить токен от "сервера" в момент авторизации, а затем каждый запрос "подписывать" этим токеном.

## The main differences from JSON-RPC 1.0, 1.1, 1.2, 2.0
В запросе должен быть минимум один обязательный параметр:

 * `method`

Остальные параметры можно пропустить или присвоить им значение `null`. Это будет равносильно отсутствию параметра.

В ответе должен быть как минимум 1 обязательных параметра:

* `result`

Главное отличие, если запрос возвращает ошибку, все равно возвращается:

* `result`

так как ошибка это тоже результат.

``"result": "error"``

Особенно это может быть важно при отправке уведомления.

Сервер отправляющий уведомление совсем не интересуется ответом или его все же может заинтересовать результат?

``"result": "success"``

or

``"result": "error"``

В связи с этим  серверу заинтересованному в `result`, так же важен и `id`. Но это только при асинхронных запросах. При синхронных запросах `id` не нужен.

## Key Benefits of JSON-RPC 1.5
Максимальная гибкост протокола и возможность передачи подписи `sign`

## Why version 1.5 alt

Очивидно, что alt это альтернатива JSON-RPC 1.0, 1.1, 1.2, 2.0

version 1.5 - потому что данный протокол взял все лучшее от протоклов 1.0 и 2.0 и находится между ними. А так же потому, что с ними не совместим.
