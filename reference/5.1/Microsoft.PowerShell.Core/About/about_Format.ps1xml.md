---
keywords: powershell,cmdlet
locale: en-us
ms.date: 02/19/2019
online version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_format.ps1xml?view=powershell-5.1&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about_Format.ps1xml
---
# About Format.ps1xml

## Short Description
The `Format.ps1xml` files in PowerShell define the default display of
objects in the PowerShell console. You can create your own
`Format.ps1xml` files to change the display of objects or to define default
displays for new object types that you create in PowerShell.

## Long Description

The `Format.ps1xml` files in PowerShell define the default display of
objects in PowerShell. You can create your own `Format.ps1xml` files to
change the display of objects or to define default displays for new object
types that you create in PowerShell.

When PowerShell displays an object, it uses the data in structured
formatting files to determine the default display of the object. The data in
the formatting files determines whether the object is rendered in a table or
in a list, and it determines which properties are displayed by default.

The formatting affects the display only. It does not affect which object
properties are passed down the pipeline or how they are passed. `Format.ps1xml`
files cannot be used to customize the output format for hashtables.

PowerShell includes seven formatting files. These files are located in
the installation directory (`$PSHOME`). Each file defines the display of a
group of Microsoft .NET Framework objects:

1. `Certificate.Format.ps1xml`

   Objects in the Certificate store, such as X.509 certificates and
   certificate stores.

2. `DotNetTypes.Format.ps1xml`

   Other .NET Framework types, such as CultureInfo, FileVersionInfo, and
   EventLogEntry objects.

3. `FileSystem.Format.ps1xml`

   File system objects, such as files and directories.

4. `Help.Format.ps1xml`

   Help views, such as detailed and full views, parameters, and examples.

5. `PowerShellCore.format.ps1xml`

   Objects generated by PowerShell core cmdlets, such as
   `Get-Member` and `Get-History`.

6. `PowerShellTrace.format.ps1xml`

   Trace objects, such as those generated by the `Trace-Command` cmdlet.

7. `Registry.format.ps1xml`

   Registry objects, such as keys and entries.

A formatting file can define four different views of each object:

- Table
- List
- Wide
- Custom.

For example, when the output of a `Get-ChildItem` command is
piped to a `Format-List` command, `Format-List` uses the view in the
"FileSystem.format.ps1xml" file to determine how to display the file and folder
objects as a list.

When a formatting file includes more than one view of an object, PowerShell applies the first view
that it finds.

In a `Format.ps1xml` file, a view is defined by a set of XML tags that describe
the name of the view, the type of object to which it can be applied, the
column headers, and the properties that are displayed in the body of the view.
The format in `Format.ps1xml` files is applied just before the data is presented
to the user.

## Creating New Format.ps1xml Files

The `.ps1xml` files that are installed with PowerShell are digitally
signed to prevent tampering because the formatting can include script blocks.
Therefore, to change the display format of an existing object view, or to add
views for new objects, create your own `Format.ps1xml` files, and then add them
to your PowerShell session.

To create a new file, copy an existing `Format.ps1xml` file. The new file can
have any name, but it must have a `.ps1xml` file name extension. You can place
the new file in any directory that is accessible to PowerShell, but it
is useful to place the files in the PowerShell installation directory
(`$PSHOME`) or in a subdirectory of the installation directory.

To change the formatting of a current view, locate the view in the formatting
file, and then use the tags to change the view. To create a view for a new
object type, create a new view, or use an existing view as a model. (The tags
are described in the next section of this topic.) You can then delete all the
other views in the file so that the changes are obvious to anyone examining
the file.

When you have saved the changes, use the `Update-FormatData` cmdlet to add the
new file to your PowerShell session. If you want your view to take
precedence over a view defined in the built-in files, use the **PrependPath**
parameter. `Update-FormatData` affects only the current
session. To make the change to all future sessions, add the
`Update-FormatData` command to your PowerShell profile.

### Example: Adding Calendar Data to Culture Objects

This example shows how to change the formatting of the culture objects
**System.Globalization.CultureInfo** generated by the `Get-Culture` cmdlet. The
commands in the example add the **Calendar** property to the default table view
display of culture objects.

The first step is to find the `Format.ps1xml` file that contains the current
view of the culture objects. The following `Select-String` command finds the
file:

```powershell
Select-String -Path $PSHOME\*format.ps1xml `
  -Pattern System.Globalization.CultureInfo
```

This command reveals that the definition is in the `DotNetTypes.Format.ps1xml`
file.

The next command copies the file contents to a new file,
`MyDotNetTypes.Format.ps1xml`.

```powershell
Copy-Item $PSHome\DotNetTypes.Format.ps1xml MyDotNetTypes.Format.ps1xml
```

Next, open the `MyDotNetTypes.Format.ps1xml` file in any XML or text editor,
such as **Notepad.exe**. Find the **System.Globalization.CultureInfo** object
section. The following XML defines the views of the **CultureInfo** object. The
object has only a **TableControl** view.

```xml
<View>
  <Name>System.Globalization.CultureInfo</Name>
  <ViewSelectedBy>
    <TypeName>Deserialized.System.Globalization.CultureInfo</TypeName>
    <TypeName>System.Globalization.CultureInfo</TypeName>
  </ViewSelectedBy>
  <TableControl>
    <TableHeaders>
      <TableColumnHeader>
        <Width>16</Width>
      </TableColumnHeader>
      <TableColumnHeader>
        <Width>16</Width>
      </TableColumnHeader>
    </TableHeaders>
    <TableRowEntries>
      <TableRowEntry>
        <TableColumnItems>
          <TableColumnItem>
            <PropertyName>LCID</PropertyName>
          </TableColumnItem>
          <TableColumnItem>
            <PropertyName>Name</PropertyName>
          </TableColumnItem>
          <TableColumnItem>
            <PropertyName>DisplayName</PropertyName>
          </TableColumnItem>
        </TableColumnItems>
      </TableRowEntry>
    </TableRowEntries>
  </TableControl>
</View>
```

Delete the remainder of the file, except for the opening `<?xml>`,
`<Configuration>`, and `<ViewDefinitions>` tags and the closing
`<ViewDefinitions>` and `<Configuration>` tags. You must also delete the
digital signature whenever you change the file.

The `MyDotNetTypes.Format.ps1xml` file should now look like the one below.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Configuration>
<ViewDefinitions>
<View>
  <Name>System.Globalization.CultureInfo</Name>
  <ViewSelectedBy>
    <TypeName>Deserialized.System.Globalization.CultureInfo</TypeName>
    <TypeName>System.Globalization.CultureInfo</TypeName>
  </ViewSelectedBy>
  <TableControl>
    <TableHeaders>
      <TableColumnHeader>
        <Width>16</Width>
      </TableColumnHeader>
      <TableColumnHeader>
        <Width>16</Width>
      </TableColumnHeader>
      <TableColumnHeader/>
    </TableHeaders>
    <TableRowEntries>
      <TableRowEntry>
        <TableColumnItems>
          <TableColumnItem>
            <PropertyName>LCID</PropertyName>
          </TableColumnItem>
          <TableColumnItem>
            <PropertyName>Name</PropertyName>
          </TableColumnItem>
          <TableColumnItem>
            <PropertyName>DisplayName</PropertyName>
          </TableColumnItem>
        </TableColumnItems>
      </TableRowEntry>
    </TableRowEntries>
  </TableControl>
</View>
</ViewDefinitions>
</Configuration>
```

Next, create a new column for the **Calendar** property by adding a new set of
`<TableColumnHeader>` tags. The value of the **Calendar** property can be long, so
specify a value of 45 characters as the `<Width>`.

```xml
<TableHeaders>
  <TableColumnHeader>
    <Width>16</Width>
  </TableColumnHeader>
  <TableColumnHeader>
    <Width>16</Width>
  </TableColumnHeader>
  <TableColumnHeader>
    <Width>45</Width>
  </TableColumnHeader>
  <TableColumnHeader/>
</TableHeaders>
```

Now, add a new column item in the table rows, as follows:

```xml
<TableRowEntries>
  <TableRowEntry>
    <TableColumnItems>
      <TableColumnItem>
        <PropertyName>LCID</PropertyName>
      </TableColumnItem>
      <TableColumnItem>
        <PropertyName>Name</PropertyName>
      </TableColumnItem>
      <TableColumnItem>
        <PropertyName>Calendar</PropertyName>
      </TableColumnItem>
      <TableColumnItem>
        <PropertyName>DisplayName</PropertyName>
      </TableColumnItem>
    </TableColumnItems>
  </TableRowEntry>
</TableRowEntries>
```

After saving the file and closing it, use the `Update-FormatData` cmdlet to
add the new format file to the current session.

This example uses the **PrependPath** parameter to place the new file in a
higher precedence order than the original file. For more information, see
[Update-FormatData](../../Microsoft.PowerShell.Utility/Update-FormatData.md).

```powershell
Update-FormatData -PrependPath $PSHOME\MyDotNetTypes.format.ps1xml
```

To test the change, type `Get-Culture`, and then review the output, which
includes the Calendar property.

```powershell
Get-Culture
```

```Output
LCID Name  Calendar                               DisplayName
---- ----  --------                               -----------
1033 en-US System.Globalization.GregorianCalendar English (United States)
```

## The XML in Format.ps1xml Files

The **ViewDefinitions** section of each `Format.ps1xml` file contains the
`<View>` tags that define each view. A typical `<View>` tag includes the
following tags:

- `<Name>` identifies the name of the view
- `<ViewSelectedBy>` specifies the object type or types to which the view
  applies
- `<GroupBy>` specifies how items in the view will be combined in groups
- `<TableControl>`, `<ListControl>`, `<WideControl>`, and `<CustomControl>`
  contain the tags that specify how each item will be displayed

### ViewSelectedBy tag

The `<ViewSelectedBy>` tag can contain a `<TypeName>` tag for each object type
to which the view applies. Or, it can contain a `<SelectionSetName>` tag that
references a selection set that is defined elsewhere by using a
`<SelectionSet>` tag.

### GroupBy tag

The `<GroupBy>` tag contains a `<PropertyName>` tag that specifies the object
property by which items are to be grouped. It also contains either a `<Label>`
tag that specifies a string to be used as a label for each group or a
`<CustomControlName>` tag that references a custom control defined elsewhere
using a `<Control>` tag. The `<Control>` tag contains a `<Name>` tag and a
`<CustomControl>` tag.

### TableControlTag

The `<TableControl>` tag typically contains `<TableHeaders>` and
`<TableRowEntries>` tags that define the formatting for the table's heads and
rows. The `<TableHeaders>` tag typically contains `<TableColumnHeader>` tags
that contain `<Label>`, `<Width>`, and `<Alignment>` tags. The
`<TableRowEntries>` tag contains `<TableRowEntry>` tags for each row in the
table. The `<TableRowEntry>` tag contains a `<TableColumnItems>` tag that
contains a `<TableColumnItem>` tag for each column in the row. Typically, the
`<TableColumnItem>` tag contains either a `<PropertyName>` tag that identifies
the object property to be displayed in the defined location, or a
`<ScriptBlock>` tag that contains script code that calculates a result that is
to be displayed in the location.

> [!NOTE]
> Script blocks can also be used elsewhere in locations where calculated
> results can be useful.

The `<TableColumnItem>` tag can also contain a `<FormatString>` tag that
specifies how the property or the calculated results will be displayed.

### ListControl tag

The `<ListControl>` tag typically contains a `<ListEntries>` tag. The
`<ListEntries>` tag contains a `<ListEntry>` tag. The `<ListEntry>` tag contains
a `<ListItems>` tag. The `<ListItems>` tag contains `<ListItem>` tags, which
contain `<PropertyName>` tags. The `<PropertyName>` tags specify the object
property to be displayed at the specified location in the list. If the view
selection is defined using a selection set, the `<ListControl>` and
`<ListEntry>` tags can also contain an `<EntrySelectedBy>` tag that contains one
or more `<TypeName>` tags. These `<TypeName>` tags specify the object type that
the `<ListControl>` tag is intended to display.

### WideControl tag

The `<WideControl>` tag typically contains a `<WideEntries>` tag. The
`<WideEntries>` tag contains one or more `<WideEntry>` tags. A `<WideEntry>`
tag typically contains a `<PropertyName>` tag that specifies the property to
be displayed at the specified location in the view. The `<PropertyName>` tag
can contain a `<FormatString>` tag that specifies how the property is to be
displayed.

### CustomControl tag

The `<CustomControl>` tag lets you use a script block to define a format. A
`<CustomControl>` tag typically contains a `<CustomEntries>` tag that contains
multiple `<CustomEntry>` tags. Each `<CustomEntry>` tag contains a
`<CustomItem>` tags that can contain a variety of tags that specify contents
and formatting of the specified location in the view, including `<Text>`,
`<Indentation>`, `<ExpressionBinding>`, and `<NewLine>` tags.

## Default Displays in Types.ps1xml

The default displays of some basic object types are defined in the
`Types.ps1xml` file in the `$PSHOME` directory. The nodes are named
**PsStandardMembers**, and the subnodes use one of the following tags:

- `<DefaultDisplayProperty>`
- `<DefaultDisplayPropertySet>`
- `<DefaultKeyPropertySet>`

For more information, see [about_Types.ps1xml](about_Types.ps1xml.md).

## Tracing Format.ps1xml File Use

To detect errors in the loading or application of `Format.ps1xml` files, use the
`Trace-Command` cmdlet with any of the following format components as the
value of the **Name** parameter:

- FormatFileLoading
- FormatViewBinding
- UpdateFormatData

For more information,
see [Trace-Command](../../Microsoft.PowerShell.Utility/Trace-Command.md)
and [Get-TraceSource](../../Microsoft.PowerShell.Utility/Get-TraceSource.md).

## Signing a Format.ps1xml File

To protect the users of your Format.ps1xml file, sign the file using a digital
signature. For more information, see [about_Signing](about_Signing.md).

## See Also

[Update-FormatData](../../Microsoft.PowerShell.Utility/Update-FormatData.md)

[Trace-Command](../../Microsoft.PowerShell.Utility/Trace-Command.md)

[Get-TraceSource](../../Microsoft.PowerShell.Utility/Get-TraceSource.md)

[Format Schema XML Reference](/powershell/scripting/developer/format/format-schema-xml-reference)

[Writing a PowerShell Formatting File](/powershell/scripting/developer/format/writing-a-powershell-formatting-file)