# JSON Responsive Convetion
Variation from [JSend](https://github.com/omniti-labs/jsend)

## Here you have some examples
A basic success example might be:
```JSON
{
	"status": {         
		"type": "success",
		"code": "200",
		"timestamp": "2020-08-11 06:25:00.123"
	},
	"data": {           
		"employers": [
			{
				"name": "Marco",
				"age": 24,
				"role": "Software Engineer"
			},
			{
				"name": "Juanito",
				"age": 27,
				"role": "Data sciencetist"
			}
		]
	}
}
```

When setting up a JSON API, you'll have all kinds of different types of calls and responses. json-responsive-convention separates responses into some basic types, and defines required and optional keys for each type:

<table>
<tr><th>Type</th><th>Description</th><th>Required Keys</th></tr>
<tr><td>success</td><td>All went well, and (usually) some data was returned.</td><td>status, data</td></tr>
<tr><td>fail</td><td>There was a problem with the data submitted, or some pre-condition of the API call wasn't satisfied</td><td>status, data</td></tr>
<tr><td>error</td><td>An error occurred in processing the request, i.e. an exception was thrown</td><td>status, message</td></tr>
</table>

## Example response types 

### Success ### 
When an API call is successful, the json-responsive-convention object is used as a simple envelope for the results, using the data key, as in the following:
#### GET /employers/2.json: ####
```JSON
{
	"status": {
		"type": "success",
		"code": "200",
		"timestamp": "2020-08-11 06:25:00.123"
	},
	"links": {
		"current_page": "http://localhost/api/v1/employers/2",
		"prev_page": "http://localhost/api/v1/employers/1",
		"next_page": "http://localhost/api/v1/employers/3"
	},
	"data": {
		"employers": [
			{
				"name": "Marco",
				"age": 24,
				"role": "Software Engineer"
			},
			{
				"name": "Juanito",
				"age": 27,
				"role": "Software Engineer"
			}
		]
	}
}
```
#### DELETE /employers/2.json: ####
```JSON
{
	"status": {
		"type": "success",
		"code": "200",
		"timestamp": "2020-08-11 06:25:00.123"
	},
	"data": null
}
```
Required keys:

* status: Should always be set to "success".
* data: Acts as the wrapper for any data returned by the API call. If the call returns no data (as in the last example), data should be set to null.
* links: An optional attribute. Contains the links for accessing to another methods.

### Fail ### 
When an API call is rejected due to invalid data or call conditions, the json-responsive-convention object's data key contains an object explaining what went wrong, typically a hash of validation errors. For example:
#### POST /employers.json (with data body: "Trying to create an employer"): ####
```JSON
{
	"status": {
		"type": "fail",
		"code": "400",
		"timestamp": "2020-08-11 06:25:00.123"
	},
	"data": {
		"message": "Field \"name\" must not be null when creating employer"
	}
}
```
Required keys:

* status: Should always be set to "fail", although code may vary.
* data: Again, provides the wrapper for the details of why the request failed. If the reasons for failure correspond to POST values, the response object's keys SHOULD correspond to those POST values.

### Error ### 
When an API call fails due to an error on the server. For example:
#### GET /employers/1.json: ####
```JSON
{
	"status": {
		"type": "error",
		"code": "504",
		"timestamp": "2020-08-11 06:25:00.123"
	},
	"data": {
		"message": "Unable to connect with database"
	}
}
```
Required keys:
* status: Should always be set to "error", although code may vary.
* message: A meaningful, end-user-readable (or at the least log-worthy) message, explaining what went wrong.

## Using HTTP response headers ##

* The official HTTP spec has 41+ status codes, and there are many interpretations on how to use each one. This convention, 
on the other hand, defines a more constrained set of status codes, specifically related to handling JSON traffic in the 
context of a dynamic web UI.
* The spec is meant to be as small, constrained, and generally-applicable as possible. As such, it has to be somewhat 
self-contained. A common pattern for implementing JSON services is to load a JavaScript file which passes a JSON block 
into a user-specified callback. JSON-over-XHR handling in many JavaScript frameworks follows similar patterns. As such, 
the end-user (developer) never has a chance to access the HTTP response itself.

So where does that leave us? Accounting for deficiencies in the status quo does not negate the usefulness of HTTP 
compliance. Therefore it is advised that server-side developers use both: provide a json-responsive-convention response 
body, and whatever 
HTTP header(s) are most appropriate to the corresponding body.

## License ##
The json-responsive-convention specification (this page) is covered under a [modified BSD License](LICENSE.md)


