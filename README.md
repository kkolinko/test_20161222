# test_20161222
Sample configuration to reproduce crash in mod_proxy_ajp in HTTPD 2.4.25.

License: Apache License 2.0<br>
See files Apache24/LICENSE.txt, Tomcat7/LICENSE

## Prerequisites

 1. As written at [http://www.apachelounge.com/download/]:

    >   Be sure that you have installed the latest C++ Redistributable for Visual
    >   Studio 2015: vc_redist_x64/86.exe.

    https://www.microsoft.com/en-us/download/details.aspx?id=53840
    (Microsoft Visual C++ 2015 Redistributable Update 3)

 2. Tomcat needs a Java Runtime.

    I am using Oracle Java 8 JRE (8u112), 32-bit

## Software

Software that is used here

 *  Apache24

    `httpd-2.4.25-win32-VC14.zip`
    from http://www.apachelounge.com/download/

    minus documentation, include headers, libraries

 *  Tomcat7
 
    `apache-tomcat-7.0.73.zip`
    from http://tomcat.apache.org/download-70.cgi

## Configuration

 1. In `Apache24/conf/*`, `Apache24/conf/extra/*`

    Replace all locations of `C:/` with actual path of this directory, e.g. `D:/test_20161222/`

 2. In `Apache24/conf/httpd.conf`

    Replace the following line:

        Listen 80

    with two lines:

        Listen 127.0.0.1:9090
        ServerName localhost

    to listen for local connections only, on port 9090 and to configure ServerName.

 3. In Tomcat7/conf/server.xml

    In <Connector> elements add `address="127.0.0.1"`
    to listen for local connections only.

 4. In Apache24/conf/httpd.conf

    Uncomment the following lines
    to enable modules mod_proxy and mod_proxy_ajp:

        LoadModule proxy_module modules/mod_proxy.so
        LoadModule proxy_ajp_module modules/mod_proxy_ajp.so

    Add the following lines to the end of the file
    to configure proxying for examples web application.

    <IfModule mod_proxy.c>
        ProxyRequests off
        <Location /examples>
            ProxyPass ajp://127.0.0.1:8009/examples
        </Location>
    </IfModule>

 5. Start the servers:

    See `run.bat`.

    Apache HTTPD:

        start "Apache" Apache24\bin\httpd.exe -w

    Apache Tomcat:

        set CATALINA_HOME=Tomcat7
        set "JAVA_HOME=C:\Program Files (x86)\Java\jre1.8.0_112"
        Tomcat7\bin\startup.bat

 6. Start a web browser and try browsing the examples web application

        http://127.0.0.1:9090/examples/

    Visiting the root page and refreshing it (F5) is enough to trigger the issue.

 7. Stop servers by closing their console windows (Ctrl+C).
