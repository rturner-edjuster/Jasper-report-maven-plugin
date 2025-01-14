[![Version](https://img.shields.io/maven-central/v/com.pro-crafting.tools/jasperreports-plugin?logo=apache-maven&style=for-the-badge)](https://search.maven.org/artifact/com.pro-crafting.tools/jasperreports-plugin)
[![GitHub Actions Status](<https://img.shields.io/github/actions/workflow/status/pro-crafting/Jasper-report-maven-plugin/build.yml?branch=main&logo=GitHub&style=for-the-badge>)](https://github.com/pro-crafting/Jasper-report-maven-plugin/actions/workflows/build.yml)

jasperreports-plugin
=============

This maven plugin compiles JasperReport report files to the target directory. 

Migration to version 4
----------

The release of [JasperReports](https://github.com/TIBCOSoftware/jasperreports) version 7.0.0 introduced significant changes, including some breaking changes. As a result, this Maven plugin requires updates, and all reports must be manually migrated using Jasper Studio. For more details on the changes, please refer to the [release notes](https://github.com/TIBCOSoftware/jasperreports/releases/tag/7.0.0).

In this version, the xmlValidation property has been removed. If your application depends on this property for XML validation, you will need to update your configuration. Please refer to the Installation Section for further details on how to adjust your setup accordingly.

Due to classpath conflicts, it is not possible to compile reports for versions below 7.0.0 and versions 7.0.0 or above using the same version of this plugin. To find the correct plugin version for your JasperReports version, please refer to the information at the bottom of this README.

Migration to version 3
----------

[alexnederlof](https://github.com/alexnederlof) is the original author of this plugin, but has not used it in years. The plugin got adopted by me ([Postremus](https://github.com/Postremus)) and migrated to the pro-crafting organization.
Main goal of the adoption is to always provide a version for the latest JasperReports release.

For this reason, when migrating from jasperreports-plugin 2.8 to 3.0, you will need to keep these things in mind:

1. First, you will need to change the groupdId in the plugin definition, as outlined in section [Usage](#usage)
2. Second, the plugin now gets compiled using jdk 17. Compatibility with java 1.8 is ensured, since the CI build now forces an `--release 8` flag. If any problems arise though, please let me know by opening an issue - we can always adjust that.

Motivation
----------
The original jasperreports-plugin from org.codehaus.mojo was a bit slow. This plugin is 10x faster. I tested it with 52 reports which took 48 seconds with the original plugin and only 4.7 seconds with this plugin.

Installation
-----
You can use the plugin by adding it to the plugin section in your pom:

```xml
<build>
	<plugins>
		<plugin>
			<groupId>com.pro-crafting.tools</groupId>
			<artifactId>jasperreports-plugin</artifactId>
			<version>3.6.0</version>
			<executions>
				<execution>
					<phase>process-sources</phase>
	   				<goals>
	      					<goal>jasper</goal>
	   				</goals>
	   			</execution>
			</executions>
			<configuration>
				<!-- These are the default configurations: -->
				<compiler>net.sf.jasperreports.jdt.JRJdtCompiler</compiler>
				<sourceDirectory>src/main/jasperreports</sourceDirectory>
				<outputDirectory>${project.build.directory}/jasper</outputDirectory>
				<outputFileExt>.jasper</outputFileExt>
				<verbose>false</verbose>
				<numberOfThreads>4</numberOfThreads>
				<failOnMissingSourceDirectory>true</failOnMissingSourceDirectory>
				<sourceScanner>org.codehaus.plexus.compiler.util.scan.StaleSourceScanner</sourceScanner>
			</configuration>
		</plugin>
	</plugins>
</build>
```

You might also need to add the following repositories section to your pom in order to correctly resolve all dependencies:

```xml
<repositories>
    <repository>
        <id>jaspersoft-third-party</id>
        <url>https://jaspersoft.jfrog.io/jaspersoft/third-party-ce-artifacts/</url>
    </repository>
</repositories>
```

JasperReports Version 6.20.2 and 6.20.3 does not include the dependency for exporting pdfs. It needs to be provided by the user.
See also issue [#351](https://github.com/TIBCOSoftware/jasperreports/issues/351) in the JasperReports repository.

This plugin already provides the openpdf library - you won't have to add it for compilation of your reports.
However, the library needs to be present during report execution/filling time, e.g. it needs to be added to your deployed application.

```xml
<dependencies>
    <dependency>
        <groupId>com.github.librepdf</groupId>
        <artifactId>openpdf</artifactId>
        <version>1.3.30.jaspersoft.2</version>
    </dependency>
</dependencies>
```

Usage
-----
If you want to pass any Jasper options to the compiler you can do so by adding them to the configuration like so:

```xml
<plugin>
	...
	<configuration>
		...
		<additionalProperties>
			<net.sf.jasperreports.awt.ignore.missing.font>true</net.sf.jasperreports.awt.ignore.missing.font>
			<net.sf.jasperreports.default.pdf.font.name>Courier</net.sf.jasperreports.default.pdf.font.name>
			<net.sf.jasperreports.default.pdf.encoding>UTF-8</net.sf.jasperreports.default.pdf.encoding>
			<net.sf.jasperreports.default.pdf.embedded>true</net.sf.jasperreports.default.pdf.embedded>
           </additionalProperties>
	</configuration>
</plugin>
```

You can also add extra elements to the classpath using

```xml
<plugin>
	...
	<configuration>
		...
		<classpathElements>
			<element>your.classpath.element</element>
        </classpathElements>
	</configuration>
</plugin>
```

You can also use this alternative approach for JARs:

```xml
<plugin>
	...
	<configuration>
		...
		<additionalClasspath>/web/lib/ServiceBeans.jar;/web/lib/WebForms.jar</additionalClasspath>
	</configuration>
</plugin>
```

JasperReports Compatibility
-----

The following table shows which JasperReports version is included during compilation of each plugin version:

| Plugin Version | JasperReports Version |
|----------------|-----------------------|
| 4.0.0          | 7.0.0                 |
| 3.6.2          | 6.21.3                |
| 3.6.1          | 6.21.2                |
| 3.6.0          | 6.21.0                |
| 3.5.10         | 6.20.6                |
| 3.5.6          | 6.20.5                |
| 3.5.5          | 6.20.4                |
| 3.5.4          | 6.20.3                |
| 3.5.3          | 6.20.2                |
| 3.5.1          | 6.20.1                |
| 3.5.0          | 6.20.0                |
| 3.4.1          | 6.19.1                |
| 3.4.0          | 6.19.0                |
| 3.3.1          | 6.18.1                |
| 3.3.0          | 6.18.0                |
| 3.2.0          | 6.17.0                |
| 3.1.0          | 6.16.0                |
| 3.0.0          | 6.15.0                |
