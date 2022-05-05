# InSecTT BLE Channel Sniff Dataset

This dataset provides Bluetooth Low Energy (BLE) measurements where a single BLE channel is observed with a Software Defined Radio (SDR) in close proximity to several BLE devices. 
Since BLE uses channel hopping, not every message is detected if only a single channel is observed. In general, we do not know the access address, the connection interval, or the hopping pattern of the connections [1]. This measurement set provides raw filtered and GFSK demodulated I/Q samples of the received BLE messages, as well as the already extracted measurements for the observed BLE connections including the header and measured timestamp. For each measurement set, we provide metadata with information about the BLE devices that are part of the setup.

In BLE there are currently two different channel hop algorithms used [1]. The first one is the Channel Selection Algorithm 1 (CSA1), which was released with the first BLE specification, and the second is the Channel Selection Algorithm 2 (CSA2) which was implemented with the BLE version 5.0. This dataset provides measurements with BLE devices using both algorithms placed near the sniffer.
The goal is to show that even though channel hopping is used, it is possible to reconstruct certain communication parameters and predict future channel access of the BLE connections by only passively listening to the channel.

This dataset is a work from [Silicon Austria Labs GmbH](https://silicon-austria-labs.com/) (SAL) and the [Institute for Communications Engineering and RF-Systems](https://www.jku.at/en/institute-for-communications-engineering-and-rf-systems/)  (NTHFS) of the Johannes Kepler University (JKU) in Linz for the [InSecTT project](https://www.insectt.eu/).

# Dataset

## Measurement Setup

The measurement setup consists of six Nordic NRF52840 devices that form three BLE connection pairs and an SDR based sniffer. The BLE devices are running the  _heart-rate monitor_  sample of the Zephyr Project [2], modified to allow configuring certain connection parameters like the connection interval and the channel map. As sniffer, we use the _HackRF One_ SDR controlled in GNURadio [3].
Here we perform a simple filtering and Gaussian Frequency Shift Keying (GFSK) demodulation of the I/Q samples of the received signal and separate the individual BLE connections by their access address. The center frequency of the sniffer is at  2.45 GHz, which corresponds to BLE channel 22. The sniffer is listening to only this channel in close proximity to the BLE devices performing their communication procedure [1] including channel hopping. 
The measurements are performed in an office environment, and thus in addition to the three BLE connection pairs, other devices are communicating.

## BLE Node Configuration
The initial dataset consists of three measurement sets with different configurations of the individual BLE connection pairs. For the first connection pair, one of the devices uses a BLE version below 5.0 and therefore CSA1 is used, while for the other two connection pairs CSA2 is used. In the following, the configuration of the individual BLE connections for the three datasets is summarized.  For the channel map the hexadecimal representation is used, where the corresponding bit entry is 1 if the channel is used and 0 otherwise.

### Measurement Set 1

For all connection pairs, a different value for the connection interval is used. For the channel map configuration 0x1FFFFFFC00 we do not allow communication on the first 10 BLE channels and for 0x1E00E00700 we only use BLE channels that do not overlap with the widely used WLAN channels 1, 6, and 11 (used channels: 8, 9, 10, 21, 22, 23, 33, 34, 35, 36).

|  Nr | CSA | connection interval | channel map |
|:--:	|:---:	|:---------------------:	|:-------------------------:	|
|  1 	|  1  	|         18.75 ms          	|        0x1FFFFFFC00       	|
|  2 	|  2  	|         12.50 ms          	|        0x1E00E00700       	|
|  3 	|  2  	|          7.50 ms         	|        0x1FFFFFFC00       	|


### Measurement Set 2
In this configuration, all connections use the same connection interval and channel map (again avoiding the main WLAN channels). 

|  Nr | CSA | connection interval | channel map |
|:--:	|:---:	|:---------------------:	|:-------------------------:	|
|  1 	|  1  	|          7.50 ms        	|        0x1E00E00700       	|
|  2 	|  2  	|          7.50 ms          	|        0x1E00E00700       	|
|  3 	|  2  	|          7.50 ms          	|        0x1E00E00700       	|


### Measurement Set 3
For the first two connection pairs no channel map is used in this configuration (channel map is 0x1FFFFFFFFF). This is a typical configuration since a lot of BLE devices have no channel blocking strategies implemented and therefore do not restrict the channel map.

|  Nr | CSA | connection interval | channel map |
|:--:	|:---:	|:---------------------:	|:-------------------------:	|
|  1 	|  1  	|          7.50  ms        	|        0x1FFFFFFFFF       	|
|  2 	|  2  	|          7.50  ms         	|        0x1FFFFFFFFF       	|
|  3 	|  2  	|         18.75  ms         	|        0x1E00E00700       	|


## Structure of Dataset
Each subfolder in the [dataset](Dataset) folder represents one measurement set for a certain scenario. Each of these sets contains a [metadata.json](dataset/set_1/metadata.json) file containing the access address of the BLE connections, the number of measurements, and the channel the sniffing was performed. 

The actual measurement of the sniffer can be found in the [AccessAddress.csv](dataset/set_1/2de79d63.csv) which contains the timestamp of the individual received messages and the corresponding header. 

The raw measurements are also provided in [raw_sniffed_bitstream.dat](dataset/set_1/raw_sniffed_bitstream.dat)


## Loading the Dataset
The measurements are provided as a CSV file which allows opening the data in various programming languages and programs. For Python we shall give a small code example to open the sniffer measurement with Pandas:


```python
#import needed packages
import pandas as pd
import numpy as np
import json

dataset_folder = "dataset/set_1"

# Load the metadate of the dataset
with open(f"{dataset_folder}/metadata.json", 'r') as fp:
    metadata = json.load(fp)
 
# load all dataframes in list
data = []

for AA in metadata["AA"]:
    df = pd.read_csv(f"{dataset_folder}/{AA}.csv") 
    data.append(df)
```

# Contact
If you have any comments, questions or feedback please contact
- Julian Karoliny: julian.karoliny@silicon-austria.com

# License 
This dataset is distributed under the [Creative Commons Attributions License 4.0 (CC-BY 4.0)](https://creativecommons.org/licenses/by/4.0/).


# Acknowledgement
This work is funded by the InSecTT project (https://www.insectt.eu/). InSecTT has received funding from the ECSEL Joint Undertaking (JU) under grant agreement No 876038. The JU receives support from the European Union’s Horizon 2020 research and innovation programme and Austria, Sweden, Spain, Italy, France, Portugal, Ireland, Finland, Slovenia, Poland, Netherlands, Turkey. The document reflects only the author’s view and the Commission is not responsible for any use that may be made of the information it contains.

# Literature

[1] Bluetooth SIG, “Bluetooth Core Specification,” Dec. 2019, v 5.2. <br/>
[2] Zephyr Project, https://github.com/zephyrproject-rtos/zephyr, 2022, Version 2.7. <br/>
[3] GNU Radio, http://www.gnuradio.org, Ver. 3.8.2.
