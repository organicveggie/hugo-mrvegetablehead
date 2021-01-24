---
title: "Aeotec Smart Switch Data"
date: 2021-01-15T18:50:23-08:00
draft: true
toc: false
images:
tags:
    - zwave
    - aeotec
    - home-assistant
---

# Manual

I managed to find a copy of the PDF manual for the switch
[here](<https://products.z-wavealliance.org/ProductManual/File?folder=&filename=Manuals/1331/Aeon%20Labs%20Smart%20Switch%206%20manual%20(2).pdf>).
That PDF included a section titled _Engineering Specifications and Advanced Functions for
Developers_, which was not present in the physical copy I received in the box. I have reproduced the
relevant sections below:

### 5.8 Configuration Set Command Class

<table>
	<thead style="text-align:center">
		<tr>
			<th>7</th>
			<th>6</th>
			<th>5</th>
			<th>4</th>
			<th>3</th>
			<th>2</th>
			<th>1</th>
			<th>0</th>
		</tr>
	</thead>
	<tbody style="text-align:center">
		<tr>
			<td colspan="8">Command Class = COMMAND_CLASS_CONFIGURATION</td>
		</tr>
		<tr>
			<td colspan="8">Command = CONFIGURATION_SET</td>
		</tr>
		<tr>
			<td colspan="8">Parameter Number</td>
		</tr>
		<tr>
			<td>Default</td><td colspan="4">Reserved</td><td colspan="3">Size</td>
		</tr>
		<tr>
			<td colspan="8">Configuration Value 1 (MSB)</td>
		</tr>
		<tr>
			<td colspan="8">Configuration Value 2</td>
		</tr>
		<tr>
			<td colspan="8">...</td>
		</tr>
	</tbody>
</table>

Parameter Number Definitions (8 bit):

| Parameter Number - Hex / Decimal | Description                                                                 | Default Value | Size |
| -------------------------------- | --------------------------------------------------------------------------- | ------------- | ---- |
| ... | ... | ... | ... |
| 0x65 (101)                       | Which reports need to send in Report group 1 (See flags in table below).    | 0x00 00 00 04 | 4    |
| 0x66 (102)                       | Which reports need to send in Report group 2 (See flags in table below).    | 0x00 00 00 08 | 4    |
| 0x67 (103)                       | Which reports need to send in Report group 3 (See flags in table below).    | 0             | 4    |
| 0x6F (111)                       | The time interval of sending Report group 1 (Valid values 0x01-0x7FFFFFFF). | 0x00 00 02 58 | 4    |
| 0x70 (112)                       | The time interval of sending Report group 2 (Valid values 0x01-0x7FFFFFFF). | 0x00 00 02 58 | 4    |
| 0x71 (113)                       | The time interval of sending Report group 3 (Valid values 0x01-0x7FFFFFFF). | 0x00 00 02 58 | 4    |

Configuration Values for parameter 101-103：

|                            |    7     |    6     |    5     |    4     |                              3                              |                              2                               |                                1                                |                               0                                |
| -------------------------- | :------: | :------: | :------: | :------: | :---------------------------------------------------------: | :----------------------------------------------------------: | :-------------------------------------------------------------: | :------------------------------------------------------------: |
| configuration Value 1(MSB) | Reserved |
| configuration Value 2      | Reserved |
| configuration Value 3      | Reserved |
| configuration Value 4(LSB) | Reserved | Reserved | Reserved | Reserved | Auto send Meter REPORT (for kWh) at the group time interval | Auto send Meter REPORT (for watt) at the group time interval | Auto send Meter REPORT (for current) at the group time interval | Auto send Meter REPORT (for voltage at the group time interval |

#### Examples:

Automatically report Meter CC (Watts) to node "1" every 12 minutes

1. Enable sending Meter CC (Watts) automatically in report group 1

```cpp
ZW_SendData(0x70, 0x0, 0x65, 0x0, 0x00, 0x00, 0x00, 0x0);
```

2. Set the interval of sending Meter CC (Watts) in report group 1

```cpp
ZW_SendData(0x70, 0x0, 0x6F, 0x0, 0x00, 0x00, 0x02, 0xd0);
```
