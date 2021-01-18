1. Create new document with new index
```
PUT hamlet_raw/_doc/1
{
 "line" : "To be, or not to be: that is the question"
}
```
1. Insert new field in existing document

**Method-1**
```
POST hamlet_raw/_update/1
{
  "script" : {
    "source": "ctx._source.line = 'To be, or not to be: that is the question'"
  }
}
```
**Method-2**
```
POST hamlet_raw/_update/1
{
  "line" : "To be, or not to be: that is the question",
  "line_number" : "3.1.36"
 }
 ```
