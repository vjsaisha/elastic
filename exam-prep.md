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
1. New document with automated id assigned
### Add a new document to `hamlet-raw`, so that the document ###
(i) has the id automatically assigned by Elasticsearch, 
(ii) has default type, 
(iii) has a field named `text_entry` with value  "Whether tis nobler in the mind to suffer"
(iv) has a field named `line_number` with value "3.1.66"
```
POST hamlet_raw/_doc
{
"text_entry" : "Whether tis nobler in the mind to suffer",
"line_number" : "3.1.66"
}
```
### Update all documents with new field "speaker" value "hamlet"
```
POST hamlet_raw/_update_by_query
{
"script" :
{
"source" : "ctx._source.speaker = 'hamlet' "
},
"query" :
{
"match_all" : {}
}
}
```
### # Update the document with id "1" by renaming the field `line` into `text_entry`
```
POST hamlet_raw/_update/1
{
  "script": {
    "source" : "ctx._source.remove('line') "
  }
}

POST hamlet_raw/_update/1
{
  "script": {
    "source": "ctx._source.text_entry = 'To be, or not to be that is the question'"
  }
}
```
