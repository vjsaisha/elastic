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
Create hamlet index
```
PUT hamlet/_doc/_bulk
{"index":{"_index":"hamlet","_id":0}}
{"line_number":"1.1.1","speaker":"BERNARDO","text_entry":"Whos there?"}
{"index":{"_index":"hamlet","_id":1}}
{"line_number":"1.1.2","speaker":"FRANCISCO","text_entry":"Nay, answer me: stand, and unfold yourself."}
{"index":{"_index":"hamlet","_id":2}}
{"line_number":"1.1.3","speaker":"BERNARDO","text_entry":"Long live the king!"}
{"index":{"_index":"hamlet","_id":3}}
{"line_number":"1.2.1","speaker":"KING CLAUDIUS","text_entry":"Though yet of Hamlet our dear brothers death"}
{"index":{"_index":"hamlet","_id":4}}
{"line_number":"1.2.2","speaker":"KING CLAUDIUS","text_entry":"The memory be green, and that it us befitted"}
{"index":{"_index":"hamlet","_id":5}}
{"line_number":"1.3.1","speaker":"LAERTES","text_entry":"My necessaries are embarkd: farewell:"}
{"index":{"_index":"hamlet","_id":6}}
{"line_number":"1.3.4","speaker":"LAERTES","text_entry":"But let me hear from you."}
{"index":{"_index":"hamlet","_id":7}}
{"line_number":"1.3.5","speaker":"OPHELIA","text_entry":"Do you doubt that?"}
{"index":{"_index":"hamlet","_id":8}}
{"line_number":"1.4.1","speaker":"HAMLET","text_entry":"The air bites shrewdly; it is very cold."}
{"index":{"_index":"hamlet","_id":9}}
{"line_number":"1.4.2","speaker":"HORATIO","text_entry":"It is a nipping and an eager air."}
{"index":{"_index":"hamlet","_id":10}}
{"line_number":"1.4.3","speaker":"HAMLET","text_entry":"What hour now?"}
{"index":{"_index":"hamlet","_id":11}}
{"line_number":"1.5.2","speaker":"Ghost","text_entry":"Mark me."}
{"index":{"_index":"hamlet","_id":12}}
{"line_number":"1.5.3","speaker":"HAMLET","text_entry":"I will."}
```
**** snapshot and script based replace is pending ****

### Create the index template `hamlet_template`, so that the template (i) matches any index that starts by "hamlet_" or "hamlet-", (ii) allocates one primary shard and no replicas for each matching index 
```
PUT _template/hamlet_template
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "index_patterns": ["hamlet_*","hamlet-*"]
}
```
### Create the indices `hamlet2` and `hamlet_test` Verify that only `hamlet_test` applies the settings defined in `hamlet_template`
```
PUT hamlet2/_doc/1
{
  "test" : "test_field"
}

PUT hamlet_test/_doc/1
{
  "test" : "test_field"
}

GET hamlet_test/_settings
GET hamlet2/_settings
```

### Update `hamlet_template` by defining a mapping for the type "_doc", so that (i) the type has three fields, named `speaker`, `line_number`, and `text_entry`, (ii) `text_entry` uses an "english" analyzer

```
POST _template/hamlet_template
{
    "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "index_patterns": ["hamlet_*","hamlet-*"],
  "mappings": {
    "properties": {
      "speaker" : {
        "type" : "keyword"
      },
      "line_number" : {
      "type": "keyword"
      },
      "text_entry" : {
        "type": "text",
        "analyzer": "english"
      }
    }
  }
}
```

#### restrict dynamic document update in template
```
POST _template/newindex 
{
  "index_patterns": ["test_*"],
  "settings": {
    "number_of_replicas": 0,
    "number_of_shards": 1
    
  },

  "mappings": {
      "dynamic" : "strict",
    "properties": {
      "name" : {
        "type": "text"
      },
      "age" : {
        "type" : "integer"
      }
    }
  }
  
}
```

### Add the dynamic



### Add aloas index
```
PUT hamlet-*/_alias/hamlet
```

## Add multiple indices and single alias in which single write index
```
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "hamlet-1",
        "alias": "hamlet",
        "is_write_index" : true
      }
    },
    {
            "add": {
        "index": "hamlet-2",
        "alias": "hamlet"
      }
    }
  ]
}
```

#### Index document with pipeline
```
POST hamlet-1/_doc?pipeline=<pipelineid>
{
"line" : 11.1.1
}
