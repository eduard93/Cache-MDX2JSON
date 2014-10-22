Cache-MDX2JSON
==============

RESTful web api for MDX2JSON transformation (also JSONP and XML/A).

Installation
-----------

1. Download zip and unpack it.
2. Run in terminal (any namespace): 

        do  ##class(%Installer.Installer).InstallFromCommandLine("{SourceDir}\Installer.cls.xml","Namespace={Namespace},SourceDir={SourceDir}",0)

  where: 
  
      {Namespace} is a namespace you want to install to. If it does not exist it would be created automatically. If it does exist only MDX2JSON package would be overwritten.
  
      {SourceDir} is a directory where you unpacked zip \ MDX2JSON (see 1).
On this step installer would create (if needed) {Namespace}, import source code and compile it, create required web application, and map MDX2JSON package to %All namespace (which will be created if does not exist).
3. Give the correct roles to /{Namespace} webapplication for it to be able to query desired cubes.


For information on how to work with this RESTful web API please refer to included documentation.
