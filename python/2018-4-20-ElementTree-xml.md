### query subnodes
[Doc of python Elementtree] 

```
<?xml version="1.0"?>
<data>
    <country name="Liechtenstein">
        <rank>1</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank>4</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
    <country name="Panama">
        <rank>68</rank>
        <year>2011</year>
        <gdppc>13600</gdppc>
        <neighbor name="Costa Rica" direction="W"/>
        <neighbor name="Colombia" direction="E"/>
    </country>
</data>
```

```
# Reading the file from disk:

import xml.etree.ElementTree as ET
tree = ET.parse('country_data.xml')
root = tree.getroot()
# Reading the data from a string:

root = ET.fromstring(country_data_as_string)

# All 'neighbor' grand-children of 'country' children of the top-level
# elements
root.findall("./country/neighbor")

```

xml:
```
<devices>
  <controller type="usb" index="0" model="ich9-ehci1">
     <address type ....../>
  </controller>
  
  <controller type="usb" index="1" model="pci-ohci">
     <address type ....../>
  </controller>
</devices>
```
update  model="pci-ohci" to model="ehci".

```
tree = ET.fromstring(xml)
ctrl=tree.findall("./devices")
for ct in ctrl:
  if str(ct.get('model')) == 'pci-ohci':
    ct.set('model', 'ehci')
return ET.tostring(tree)

```

  [Doc of python Elementtree]:https://docs.python.org/2/library/xml.etree.elementtree.html
