# XML Resolver
The `XMLResolver` provides the capability to store DNS records as XML files.

The XSD file is in the root of the project directory.

## File structure
The object MUST declare the `<name>`, `<default-ttl>` and `<resource-records>` tags.
Each `<resource-record>` within `<resource-records>` can have the following properties:
 * **name** - optional string, if none or an `@` is specified, the name will default to the zone name.
 This does not need to be a fully qualified name, as the parent will be automatically appended.
 * **ttl** - optional int, if none is specified it will default to the `<default-ttl>`.
 * **type** - required string, the RDATA type. This MUST be specified.
 * **rdata** - required
 
### Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<domain
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="../php-dns-server.xsd"
>
    <name>example.com.</name>
    <default-ttl>7200</default-ttl>

    <resource-records>
        <resource-record>
            <name>@</name>
            <ttl>10800</ttl>
            <type>SOA</type>
            <class>IN</class>
            <rdata>
                <mname>example.com.</mname>
                <rname>postmaster</rname>
                <serial>2</serial>
                <refresh>3600</refresh>
                <retry>7200</retry>
                <expire>10800</expire>
                <minimum>3600</minimum>
            </rdata>
        </resource-record>

        <resource-record>
            <type>A</type>
            <rdata>
                <address>12.34.56.78</address>
            </rdata>
        </resource-record>

        <resource-record>
            <type>A</type>
            <rdata>
                <address>90.12.34.56</address>
            </rdata>
        </resource-record>

        <resource-record>
            <type>AAAA</type>
            <rdata>
                <address>2001:acad:ad::32</address>
            </rdata>
        </resource-record>

        <resource-record>
            <name>www</name>
            <type>cname</type>
            <rdata>
                <target>@</target>
            </rdata>
        </resource-record>

        <resource-record>
            <name>@</name>
            <type>MX</type>
            <rdata>
                <preference>15</preference>
                <exchange>mail</exchange>
            </rdata>
        </resource-record>

        <resource-record>
            <name>*.subdomain</name>
            <type>A</type>
            <rdata>
                <address>192.168.1.42</address>
            </rdata>
        </resource-record>
    </resource-records>
</domain>
```

## Wildcard Domains
Wildcard domains are supported, in the example above, a query for `foobar.subdomain.example.com.` will
return `192.168.1.42`.

## Processing Records
```php
$files = [__DIR__.'/example.com.xml', __DIR__.'/test.com.xml'];
$resolver = new yswery\DNS\Resolver\XmlResolver($files);
$resolver->getAnswer(/*some query*/);
```