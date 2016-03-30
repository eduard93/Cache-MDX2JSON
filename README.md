Cache-MDX2JSON
==============

RESTful web api for MDX2JSON transformation (also JSONP and XML/A). Also supports requests about Dashboards and Widgets. Supports Caché 2014.1+.

Installation
-----------

1. Download Installer.cls.xml (from MDX2JSON folder in repository or releases page) and import it into any namespace (via Studio or SMP or `$System.OBJ.Load()`) 
2. Run in terminal (import namespace) under user with %All role: 

        Do ##class(MDX2JSON.Installer).setup()
        
Offline Installation
-----------

1. Download zip and unpack it.
2. Import Installer.cls.xml (from MDX2JSON folder in unpacked archive) and import it into any namespace (via Studio or SMP or `$System.OBJ.Load()`)
3. Run in terminal (same namespace as 2) under user with %All role: 

        Set pVars("SourceDir") = {SourceDir}
        Do ##class(MDX2JSON.Installer).setup(.pVars)

  where: 
    
      {SourceDir} is a directory where you unpacked zip \ MDX2JSON (see 1).


For information on how to work with this RESTful web API please refer to included documentation.

What Installer does
-----------
Regardless of installation method chosen, here's the list (by the order of appearance) of what installer does:

1. If `Namespace` variable is undefined, set it to `MDX2JSON`
2. Create `MDX2JSON` role
2. If `Namespace` does not exist then create it
3. If `SourceDir` is provided then import and compile all files from there. Otherwise download import and compile all required files from [GitHub](https://github.com/intersystems-ru/Cache-MDX2JSON/)
4. If `/Namespace` web application does not exist then create it and give it MDX2JSON role
5. If [%All namespace](http://docs.intersystems.com/cache20152/csp/docbook/DocBook.UI.Page.cls?KEY=GSA_config#GSA_config_namespace_addmap_all) (used for mapping purposes) does not exist then create it and map MDX2JSON package and ^MDX2JSON global there
6. Map MDX2JSON package and ^MDX2JSON global into SAMPLES namespace
7. If `User` and `Password` variables are provided, then create User and give him MDX2JSON role

Additional installation parameters
-----------

As a first parameter to `Do ##class(MDX2JSON.Installer).setup(.pVars)` you can pass pVars - a local array of additional variables (see sample in Offline Installation step 3).

- `Namespace` is a namespace you want to install MDX2JSON to (Not namespace with dashes). If it does not exist it would be created automatically. If it does exist only MDX2JSON package would be overwritten. WebApplication would be named `/Namespace`. Strongly not recommended to change the default. [MDX2JSON]
- `User` is a Caché user to create or modify. He will be given SELECT access to %DeepSee_Dashboard.Definition table in `Namespace`
- `Password` must be supplied alongside User parameters
- `SourceDir` - all xmls from this directory would be imported and compiled


Update
-----------

1.  Run in terminal (namespace where you installed MDX2JSON): 

        Do ##class(MDX2JSON.Installer).Update()
	  
You can also supply parameters such as fork, desired branch/commit, target namespace, authorization information. Please refer to Caché documentation of MDX2JSON.Installer class for correct syntax. 

Uninstall
-----------

1.  Run in terminal (any namespace from where MDX2JSON package can be accessed): 

        Do ##class(MDX2JSON.Installer).Uninstall()
	  
This action would delete MDX2JSON namespace, database (with physical directory) and web application. Mappings and `%DB_MDX2JSON` role and resource would also be deleted.

Requests
-----------

These are the possible requests to web application (add param ?Namespace={Desired Namespace} to query another namespace cubes

| URL                         | Type | Body (JSON)                 | Response  | Description                    |
|-----------------------------|------|-----------------------------|-----------|--------------------------------|
| MDX                         | POST | { "MDX":"QUERY" }           | JSON      | Results of MDX execution       |
| MDX2JSONP                   | POST | { "MDX":"QUERY" }           | JSONP     | Results of MDX execution       |
| MDXDrillthrough             | POST | { "MDX":"QUERY" }           | JSON      | Results of MDX execution       |
| MDX2XMLA                    | POST | { "MDX":"QUERY" }           | XMLA      | Results of MDX execution       |
| Dashboards                  | GET  |                             | JSON      | All dashboards                 |
| Dashboards                  | POST | {Folder:"FolderName"}       | JSON      | All dashboards in FolderName. Empty FolderName for root scope.|
| Dashboard                   | POST | {Dashboard:"DashboardName"} | JSON      | All widgets in a dashboard, with filters as part of dashboard|
| Widgets                     | POST | {Dashboard:"DashboardName"} | JSON      | All widgets in a dashboard, with filters as part of widgets  |
| DataSource                  | POST |{DataSource:"Pivot fullname"}| JSON      | All info about Pivot           |
| Action/:Cube/:Action        | POST |{[context object](http://docs.intersystems.com/ens20152/csp/docbook/DocBook.UI.Page.cls?KEY=D2IMP_ch_action#D2IMP_action_context_info)}             | JSON      | Execute cube action            |
| Filters                     | POST |{ "DataSource": "DataSourceName.ext", "Values":1, Search:"SearchTerm"}| JSON   | All filters for DeepSee DataSource (cube, pivot, kpi, metric) with values (if Values = 1, set to 0 or omit otherwise). If Search is not empty only filter values, containing search term would be returned.|
| Format                      | GET  |                             | JSON      | Default formatting             |
| TermList                    | POST | {"TermList":"TermListName"} | JSON      | Termlist key-value array       |
| Config                      | POST |{"Application":"AppName", "Config","value"}| JSON      | Set config for arbitrary application for current user|
| Config/:Application         | GET  | {"Application":"AppName"}   | JSON      | Get config for Application for current user|
| Favorites                   | GET  |                             | JSON      | Array of current user favorites|
| Favorites/:Item             | POST |                             | JSON      | Add favorite item              |
| Favorites/:Item             |DELETE|                             | JSON      | Remove item from favorites     |
| Test                        | GET  |                             | JSON      | Test info                      |
| Logout                      | GET  |                             | JSON      | Close session                  |

Example
-----------

Request URL: http://localhost:57772/MDX2JSON/MDX?Namespace=Samples

Request type: POST

Request body:

    {"MDX": "SELECT NON EMPTY [Product].[P1].[Product Category].Members ON 0,NON EMPTY [Outlet].[H1].[Region].Members ON 1 FROM [HoleFoods]"} 
    
[Result](https://github.com/intersystems-ru/Cache-MDX2JSON/wiki/MDX-output)

Please note that corresponding cube must be compiled and built beforehand.

Localization
-----------

If requested data or meta-information is available in several different languages, you can choose one, by supplying Accept-Language header to your HTTP request, formed in accordance with [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4). 

Troubleshooting
-----------

If something goes wrong, server must report an error in the following format {Error : "Error description"}, and usually that is a good indicator of what went wrong. If you received an error in another format or an error without "Error description" please file an issue [here](https://github.com/intersystems-ru/Cache-MDX2JSON/issues/8).

| Problem                     | Solution                         | 
|-----------------------------|----------------------------------|
| CSP Error                   | User does not have enough rights. Configure User or Webapplication roles  | 
| Authenticated access        | Web application must have password access, resource for database with MDX2JSON must have public RW rights enabled|
| No dashboards               | Configure roles. Change [dashboard scope](https://github.com/intersystems-ru/Cache-MDX2JSON/issues/10)|
| DeepSee errors              | Build and compile DeepSee cube(s)|
| MDX errors                  | Don't forget to escape JSON strings [here](http://json.org/string.gif) |
| Installation errors		  | Usually problems arise when installation is run under user without %All permissions. To repair the install rerun it under correct user. If the error persists then file an issue with installation log (terminal output) attached|


Debugging
-----------

Use $$$Debug macro. It would evaluate as true only if there is a "Debug" URL parameter present. Example request URL:

        http://localhost:57772/MDX2JSON/MDX?Namespace=Samples&Debug
		
Use with post conditional expressions, or other flow control statements:

		w:$$$Debug "debugging"
		if $$$Debug { w "debugging" } else { w "not debugging"}
		
Also available are $$$Public and $$$Private macros. Evaluates to true based on request port (80 and 443 are public, private otherwise).

Querying
-----------

Querying this project is done via REST web client. It may be a standalone application or a browser plug-in, a number of different solutions are available.

For Google Chrome, install [Advanced REST client](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo?hl=en) extension.
For Firefox, install [REST client](https://addons.mozilla.org/en-US/firefox/addon/restclient/) extension.
Open installed extension and set the following parameters:

- URL to required web api method, e.g.: `http://serverip:port/mdx2json/Dashboard?Namespace=Samples`
- Request type to `GET` or `POST`
- Payload to `{"Dashboard":"Listing with Filters.dashboard"}`
- Content-Type to `application/json` (only in Advanced REST client)

Press Send button to view results (depending on your server configuration you may be asked to provide valid login/password to access MDX2JSON api).

Development
-----------

To develop MDX2JSON you need:

1. Install MDX2JSON in `MDX2JSON` namespace
2. Install [Cache-Tort-Git](https://github.com/intersystems-ru/cache-tort-git)
3. In terminal, `MDX2JSON` namespace execute:

		set ^Git("settings","hook") = $lb("MDX2JSON.Tests","OnCommit")
		set ^Git("settings","groupByFolder") = 1

4. Activate Cache-Tort-Git for `MDX2JSON` namespace
5. Commit all changes via Studio