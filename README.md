# Couchdb.jl
A Couchdb client for Julia
---
# Installation
```
pkg> add https://github.com/setexams/couchdb.jl
julia> Pkg.add("https://github.com/setexams/couchdb.jl")
```
---
# Usage
---
Create an instance of the connection struct
```
julia> using Couchdb

julia> conn = Couchdb.CouchdbConn("scheme", "admin", "password", "host", 5984)
Main.Couchdb.CouchdbConn("http", "admin", "password", "127.0.0.1", 5984)

```

Get information about the server
```
julia> Couchdb.server_info(conn)
Dict{String, Any} with 6 entries:
  "git_sha"  => "d5b746b7c"
  "features" => Any["access-ready", "partitioned", "pluggable-storage-engines", "reshard", "scheduler"]
  "uuid"     => "bd95286d4f15e8db2f18d2a4ffa35053"
  "couchdb"  => "Welcome"
  "version"  => "3.2.2"
  "vendor"   => Dict{String, Any}("name"=>"The Apache Software Foundation")

```
Get a list of all databases in the couchdb instance

Example 
```

julia> Couchdb.all_dbs(conn)
4-element Vector{Any}:
 "_replicator"
 "_users"
 "test"

julia> 
```
Get information about a database

Example
```
julia> Couchdb.db_info(conn, "test")
Dict{String, Any} with 11 entries:
  "purge_seq"           => "0-g1AAAABXeJzLYWBgYMpgTmEQTM4vTc5ISXIwNDLXMwBCwxyQVB4LkGRoAFL_gSArkQGP2kSGpHqIoiwAtOgYRA"
  "props"               => Dict{String, Any}()
  "db_name"             => "test"
  "update_seq"          => "20-g1AAAACbeJzLYWBgYMpgTmEQTM4vTc5ISXIwNDLXMwBCwxyQVB4LkGRoAFL_gSArgzmRMxcowG6QaGJqammATR8e0xIZkuqhxnCDjTEzMDFPNknGpiELAMveJ_0"
  "sizes"               => Dict{String, Any}("external"=>1429102, "active"=>1438660, "file"=>1581471)
  "disk_format_version" => 8
  "doc_count"           => 7
  "compact_running"     => false
  "cluster"             => Dict{String, Any}("w"=>1, "r"=>1, "q"=>2, "n"=>1)
  "instance_start_time" => "0"
  "doc_del_count"       => 1

```
create a databse

Example
```
julia> Couchdb.create_Db(conn, "test")
HTTP.Exceptions.StatusError(412, "PUT", "/test", HTTP.Messages.Response:
"""
HTTP/1.1 412 Precondition Failed
Cache-Control: must-revalidate
Content-Length: 95
Content-Type: application/json
Date: Sat, 31 Dec 2022 05:40:11 GMT
Server: CouchDB/3.2.2 (Erlang OTP/24)
X-Couch-Request-ID: e4bbf8f9f3
X-CouchDB-Body-Time: 0

{"error":"file_exists","reason":"The database could not be created, the file already exists."}
""")

julia> Couchdb.create_db(conn, "test1")
Dict{String, Any} with 1 entry:
  "ok" => true

julia> Couchdb.create_db(conn, "test2", Dict("partitioned"=>true))
Dict{String, Any} with 1 entry:
  "ok" => true
```
delete a database

Example 
```
julia> Couchdb.delete_db(conn, "test1")
Dict{String, Any} with 1 entry:
  "ok" => true

```
get all docs
```
julia> Couchdb.get_all_docs(conn, "test")
Dict{String, Any} with 3 entries:
  "rows"       => Any[Dict{String, Any}("key"=>"1", "id"=>"1", "value"=>Dict{String, Any}("rev"=>"6-759c5affed115dd7c03f4e31243b08e2")), Dict{String, Any}("key"=>"2", "id"=>"2", "value"=>Dict{String, Any}("rev"…
  "offset"     => 0
  "total_rows" => 7

```
get design docs
```
julia> Couchdb.get_design_docs(conn, "test")
Dict{String, Any} with 3 entries:
  "rows"       => Any[Dict{String, Any}("key"=>"_design/t2", "id"=>"_design/t2", "value"=>Dict{String, Any}("rev"=>"2-6f2fd6810e51a0e91f095f1572526c75")), Dict{String, Any}("key"=>"_design/test", "id"=>"_design…
  "offset"     => 2
  "total_rows" => 2
```
get doc from a db

Example
```
julia> Couchdb.get_doc(conn, "test","1")
Dict{String, Any} with 4 entries:
  "_rev"         => "6-759c5affed115dd7c03f4e31243b08e2"
  "name"         => "1"
  "_id"          => "1"
  "_attachments" => Dict{String, Any}("testing_name"=>Dict{String, Any}("length"=>6396, "content_type"=>"text/plain", "stub"=>true, "digest"=>"md5-LqmJzhO7mhgAwQSKy2cmEQ==", "revpos"=>3), "c025f2057955cbd2fbbfa…

```
Create a document with random id, to create with a specified id, Id must be supplied in the Dict of the doc
```
julia> Couchdb.create_doc(conn, "test", Dict("_id"=>"10"))
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "1-967a00dff5e02add41819138abb3284d"
  "id"  => "10"

julia> Couchdb.create_doc(conn, "test", Dict("key"=>"value"))
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "1-59414e77c768bc202142ac82c2f129de"
  "id"  => "a85020514daa48a66e529b13c200090b"
```
batch create docs
```
julia> batch_create_docs(conn, "test", [Dict("_id"=>"test_id"), ...])
julia> batch_create_docs(conn, "test", [Dict("key1"=>"value1"), ...])
```
this is the same procedure for bulk updating, pass the \_rev field on the docs to be updated

bulk get documents
```
julia> bulk_get_docs(conn, db, [Dict("id" => "test_id", "_rev"=>"optional_rev"), ...])
```
get a \_rev of a document

Example
```
julia> Couchdb.get_rev(conn, "test","1")
"6-759c5affed115dd7c03f4e31243b08e2"

```
delete a document

Example
```
julia> Couchdb.delete_doc(conn, "test", "10")
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "2-eec205a9d413992850a6e32678485900"
  "id"  => "10"

julia> Couchdb.get_doc(conn, "test", "10")
HTTP.Exceptions.StatusError(404, "GET", "/test/10", HTTP.Messages.Response:
"/""
HTTP/1.1 404 Not Found
Cache-Control: must-revalidate
Content-Length: 41
Content-Type: application/json
Date: Sat, 31 Dec 2022 05:56:04 GMT
Server: CouchDB/3.2.2 (Erlang OTP/24)
X-Couch-Request-ID: b1a3888aff
X-CouchDB-Body-Time: 0

{"error":"not_found","reason":"deleted"}
"/"")

```
create attachment
the attachment is saved with its extension

Example
```
julia> Couchdb.create_attachment(conn, "test", "1", "test.txt")
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "7-69b35d734138e80a43f9eb1fb2d52fb4"
  "id"  => "1"
  
julia> Couchdb.create_attachment(conn, "test", "1", "test.txt", "test.txt")
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "8-15055f1721165df731cf1b85ffe451d5"
  "id"  => "1"

```
get the attachments of a document

Example
```
julia> Couchdb.get_attachments(conn, "test","1")
Dict{String, Any} with 7 entries:
  "testing_name"                         => Dict{String, Any}("length"=>6396, "content_type"=>"text/plain", "stub"=>true, "digest"=>"md5-LqmJzhO7mhgAwQSKy2cmEQ==", "revpos"=>3)
  "c025f2057955cbd2fbbfa27a5f00bcb9"     => Dict{String, Any}("length"=>6396, "content_type"=>"text/plain", "stub"=>true, "digest"=>"md5-LqmJzhO7mhgAwQSKy2cmEQ==", "revpos"=>2)
  "c025f2057955cbd2fbbfa27a5f00e182.gif" => Dict{String, Any}("length"=>1410242, "content_type"=>"image/gif", "stub"=>true, "digest"=>"md5-CqyPmFJkOMOsMOR5yWWvzg==", "revpos"=>6)
  "test.txt"                             => Dict{String, Any}("length"=>9, "content_type"=>"text/plain", "stub"=>true, "digest"=>"md5-t3B5RxNSql1E2Uk8/UofKw==", "revpos"=>8)
  "test1.toml"                           => Dict{String, Any}("length"=>6613, "content_type"=>"application/toml", "stub"=>true, "digest"=>"md5-039BPiAt593Zs/cKqe4hOQ==", "revpos"=>5)
  "test.toml"                            => Dict{String, Any}("length"=>6613, "content_type"=>"application/toml", "stub"=>true, "digest"=>"md5-039BPiAt593Zs/cKqe4hOQ==", "revpos"=>4)
  "a85020514daa48a66e529b13c200182c.txt" => Dict{String, Any}("length"=>9, "content_type"=>"text/plain", "stub"=>true, "digest"=>"md5-t3B5RxNSql1E2Uk8/UofKw==", "revpos"=>7)

```
delete an attachmant

Example
```
julia> Couchdb.delete_attachment(conn,"test","1","test.txt")
Dict{String, Any} with 3 entries:
  "ok"  => true
  "rev" => "9-2cfb3461c65531a5afbef0dcc60fab70"
  "id"  => "1"

```
get couchdb generated uuids

Example 
```
julia> Couchdb.uuid(conn)
"a85020514daa48a66e529b13c200277e"

julia> Couchdb.uuid(conn, 10)
10-element Vector{Any}:
 "a85020514daa48a66e529b13c20033e8"
 "a85020514daa48a66e529b13c2003f6c"
 "a85020514daa48a66e529b13c2004587"
 "a85020514daa48a66e529b13c2004a21"
 "a85020514daa48a66e529b13c20053d1"
 "a85020514daa48a66e529b13c2005fd7"
 "a85020514daa48a66e529b13c2006463"
 "a85020514daa48a66e529b13c2006f16"
 "a85020514daa48a66e529b13c200709c"
 "a85020514daa48a66e529b13c200804c"

```
get a design doc

Example
```
julia> Couchdb.get_design_doc(conn, "test","test")
Dict{String, Any} with 4 entries:
  "_rev"     => "6-813847f3c826514d9c039e9fcab03efd"
  "views"    => Dict{String, Any}("test"=>Dict{String, Any}("map"=>"fun({Doc}) ->\n  Id = proplists:get_value(<<\"_id\">>, Doc),\n  Emit(Id, #{<<\"_id\">> => <<\"1\">> })\nend."))
  "_id"      => "_design/test"
  "language" => "erlang"

```
create a design doc, doc is a Dict 
a design has the following fields
```
doc=Dict(
"language" => "javascript" | "erlang"
"validate_doc_update" => "String"
"autoupdate" => "bool"
"options" => Dict(...)
"views" => Dict(...)
"updates" => Dict(...)
"filters" => Dict(...)
)
```

note that a design doc can be created by create\_doc/3 and passing "\_design/doc\_id" as doc id

invoke a view
```
options = Dict(
"include_docs" => bool,
"descending" => bool,
"endkey" => ,
"endkey_docid" => ,
"reduce" => bool,
"key" => ,
"keys" => [...],
"limit" => int,
"reduce" => bool,
"skip" => int,
"sorted" => bool,
"stable" => bool,
"startkey" => ,
"update" => true, ...
)
```

Example 
```
julia> Couchdb.invoke_view(conn, "test","test", "test", Dict())
Dict{String, Any} with 3 entries:
  "rows"       => Any[Dict{String, Any}("key"=>"1", "id"=>"1", "value"=>Dict{String, Any}("_id"=>"1")), Dict{String, Any}("key"=>"2", "id"=>"2", "value"=>Dict{String, Any}("_id"=>"1")), Dict{String, Any}("key"=…
  "offset"     => 0
  "total_rows" => 6

julia> Couchdb.invoke_view(conn, "test","test", "test", Dict("limit"=> 1, "descending"=> true))
Dict{String, Any} with 3 entries:
  "rows"       => Any[Dict{String, Any}("key"=>"a85020514daa48a66e529b13c200090b", "id"=>"a85020514daa48a66e529b13c200090b", "value"=>Dict{String, Any}("_id"=>"1"))]
  "offset"     => 0
  "total_rows" => 6
```

pass keys as an array
TODO implement multiple queries

Example 
```
Couchdb.invoke_view(conn, "test","test", "test", ["1"],Dict())
Dict{String, Any} with 3 entries:
  "rows"       => Any[Dict{String, Any}("key"=>"1", "id"=>"1", "value"=>Dict{String, Any}("_id"=>"1"))]
  "offset"     => 0
  "total_rows" => 6

julia> Couchdb.invoke_view(conn, "test","test", "test", ["10"],Dict())
Dict{String, Any} with 3 entries:
  "rows"       => Any[]
  "offset"     => 1
  "total_rows" => 6
```

# Uninstallation
```
pkg> rm Couchdb
```
