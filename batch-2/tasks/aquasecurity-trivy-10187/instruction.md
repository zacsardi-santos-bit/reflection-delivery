I'm working on a Maven dependency scanner that reads Maven settings files to find repository configurations.

*   The Proxy struct must have the following string fields: ID, Active, Protocol, Host, Port, Username, Password, and NonProxyHosts.

*   The settings struct must include a Proxies field of type []Proxy that is populated by parsing proxy entries from Maven settings XML files.

*   The readSettings() function must parse proxy definitions from both user-level ($HOME/.m2/settings.xml) and global-level ($MAVEN_HOME/conf/settings.xml) settings files and include them in the returned settings struct.

*   When merging proxies from user and global settings, duplicate entries (matched by proxy ID) must be deduplicated with the user settings proxy taking precedence over the global settings proxy.

*   When a settings file exists but contains no proxy entries, the Proxies field must be an initialized empty slice (not nil).

*   The effectiveProxies(protocol, hostname string) method on settings must return only proxies whose Active field is 'true' or empty string (treated as active by default) and whose Protocol matches the requested protocol (comparison is case-insensitive).

*   The effectiveProxies method must exclude proxies where the Active field is 'false'.

*   The effectiveProxies method must exclude proxies where the given hostname matches any pattern in the proxy's NonProxyHosts field. NonProxyHosts patterns are separated by '|' and support glob-style matching (e.g., *.example.com).

*   The effectiveProxies method must return nil (not an empty slice) when no proxies match the given protocol and hostname criteria.

*   Test data files must exist at testdata/settings/user-with-proxy/.m2/settings.xml (user settings with an HTTP proxy on host user.proxy.com:8080 with credentials and nonProxyHosts) and testdata/settings/global-with-proxy/conf/settings.xml (global settings with an HTTP proxy on host foo.proxy.com:8080 with no credentials), both under pkg/dependency/parser/java/pom/.


*   Interface details: Type: Struct
Name: Proxy
Location: pkg/dependency/parser/java/pom/settings.go
Description: Represents a Maven proxy configuration entry parsed from a settings XML file.
Fields:
  ID            string  (xml tag: "id")
  Active        string  (xml tag: "active")
  Protocol      string  (xml tag: "protocol")
  Host          string  (xml tag: "host")
  Port          string  (xml tag: "port")
  Username      string  (xml tag: "username")
  Password      string  (xml tag: "password")
  NonProxyHosts string  (xml tag: "nonProxyHosts")

Type: Struct field addition
Name: Proxies
Location: pkg/dependency/parser/java/pom/settings.go
Description: New field added to the existing settings struct. Holds the list of proxy entries parsed from a Maven settings XML file.
Field declaration: Proxies []Proxy  (xml tag: "proxies>proxy")

Type: Method
Name: effectiveProxies
Location: pkg/dependency/parser/java/pom/settings.go
Signature: func (s settings) effectiveProxies(protocol, hostname string) []Proxy
Description: Returns the subset of configured proxies that are active, match the given protocol (case-insensitive), and are not excluded for the given hostname. A proxy with an empty Active field is treated as active. NonProxyHosts patterns are pipe-separated and support glob matching. Returns nil when no proxies match.

Type: Test data file
Name: user-with-proxy settings
Location: pkg/dependency/parser/java/pom/testdata/settings/user-with-proxy/.m2/settings.xml
Description: Maven settings XML file for user-level proxy tests. Must define localRepository as "testdata/user/repository" and one proxy entry with: id=proxy-http, active=true, protocol=http, host=user.proxy.com, port=8080, username=user-proxy-user, password=user-proxy-pass, nonProxyHosts=localhost|*.internal.com.

Type: Test data file
Name: global-with-proxy settings
Location: pkg/dependency/parser/java/pom/testdata/settings/global-with-proxy/conf/settings.xml
Description: Maven settings XML file for global-level proxy tests. Must define localRepository as "testdata/repository" and one proxy entry with: id=proxy-http, active=true, protocol=http, host=foo.proxy.com, port=8080. No username, password, or nonProxyHosts entries.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.