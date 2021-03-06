%META:TOPICINFO{author="ChadLaJoie" date="1238658161" format="1.1"
version="1.2"}% %META:TOPICPARENT{name="AuthorizationFramework"}%

Authorization Service: INI Configuration File Syntax
====================================================

Description
-----------

The authorization service uses standard INI files for most of its
configuration. INI files are basically key/value property files with the
additional concept of grouping or sections in order to provide some
scoping mechanisms to the properties.

File Syntax
-----------

An INI file contains three basic types of information; comments,
sections, and properties.

A comment is a single line starting with the characters '#' (hash) or
';' (semicolon). It is not interpreted by the parser. The following
example gives an INI file with just a comment in it.

::

    # Start of INI file

A section is always declared between '[' and ']' characters. The string
betwixt those characters are the section name. Section names should only
include alphanumeric characters plus '.' (period), '\_' (underscore),
and '-' (hyphen). One section ends when another section begins. The
following example gives an INI file with comments and sections in it.

::

    # Start of INI file
    [SECTION1]
    # Start of section named SECTION1
    # End of section named SECTION1
    [section-2]
    # Start of section named section-2

A property is a key value pair with the key being declared first
followed be an '=' (equal) and then the value. Keys should only include
alphanumeric characters plus '.' (period), '\_' (underscore), and '-'
(hyphen) while values may include any visible ASCII character plus a
space or tab. Properties belong to the section in which they are
declared.

The following example gives an INI file with comments, sections, and
properties.

::

    # Start of INI file
    [SECTION1]
    # Start of section named SECTION1
    prop1 = value1
    prop2 = value2
    # End of section named SECTION1
    [section-2]
    # Start of section named section-2
    prop2 = value3
