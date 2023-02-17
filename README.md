
# bsc-snapshots


## Database after Ancient Data Prune:

Ancient Data Prune is a new feature in [bsc v1.1.8](https://github.com/binance-chain/bsc/releases/tag/v1.1.8)


### Endpoint


[geth-20230210.tar.lz4
](https://pub-c0627345c16f47ab858c9469133073a8.r2.dev/geth-20230210.tar.lz4 
)

MD5 checksum: abd1418ab2f4155373e943e94dad0026



## Snapshots Provided by Community

Special thanks to [BNB48Club](https://twitter.com/bnb48club) on contributing another dump of snapshot, you can also refer [here](https://github.com/BNB48Club/bsc-snapshots) to download.



### Usage 

Step 1: Preparation
- Make sure your hardware meets the [suggested requirement](https://docs.binance.org/smart-chain/developer/fullnode.html).
- A disk with enough free storage, at least twice the size of the snapshot.

Step 2: Download && Uncompress
- Copy the above snapshot URL.
- Download:  `wget -O geth.tar.lz4  "<paste snapshot URL here>"` . It will take one or two hours to download the snapshot, you can put it in the backgroud by `nohup wget -O geth.tar.gz  "<paste snapshot URL here?" &`


*If you need to speedup download, just use `aria2c`*
```
aria2c -o geth.tar.lz4 -s14 -x14 -k100M https://download.bsc-snapshot.workers.dev/{filename} -o geth.tar.lz4
```
But aria2c may fail sometimes, you need to rerun the download command. To make it convient, you can use the follow script
```
#!/bin/bash
#download_snapshot.sh
#Useage: download_snapshot.sh {filename} {URI}

if [ $# -eq 2 ]; then 
        status=-1
        while (( status != 0 ))
        do 
                PIDS=`ps -ef |grep aria2c |grep -v grep | awk '{print $2}'`
                if [ "$PIDS" = "" ]; then
                        aria2c -o $1 -s14 -x14 -k100M $2
                fi
                status=$?
                pid=$(pidof aria2c)
                wait $pid 
                echo aria2c exit.
                case $status in 
                        3)
                                echo file not exist.
                                exit 3
                                ;;
                        9)
                                echo No space left on device.
                                exit 9
                                ;;
                        *)
                                continue
                                ;;
                esac
        done
else 
        echo "please input target filename and download URI"
        exit 1
fi
echo download succeed.
exit 0
```


- Uncompress: `tar -I lz4 -xvf geth.tar.lz4`. It will take more than two hours to uncompress. You can put it in the backgroud by `nohup tar -I lz4 -xvf geth.tar.lz4 &`
- You can combine the above steps by running a script:
```
wget -O geth.tar.lz4  "<paste snapshot URL here>"
tar -I lz4 -xvf geth.tar.lz4
```


- If you do not need to store the archive for use with other nodes, you may also extract it while downloading to save time and disk space:
```
wget -q -O - <snapshot URL> | tar -I lz4 -xvf -
```


Step 3: Replace Data
- First, stop the running bsc client if you have one by `kill {pid}`, and make sure the client has shut down.
- Consider backing up the original data: `mv ${BSC_DataDir}/geth/chaindata ${BSC_DataDir}/geth/chaindata_backup; mv ${BSC_DataDir}/geth/triecache ${BSC_DataDir}/geth/triecache_backup`
- Replace the data: `mv server/data-seed/geth/chaindata ${BSC_DataDir}/geth/chaindata; mv server/data-seed/geth/triecache ${BSC_DataDir}/geth/triecache`
- Start the bsc client again and check the logs


## Erigon-BSC Snapshot

> erigon version [v2.32.0](https://github.com/ledgerwatch/erigon/releases/tag/v2.38.0)
### Endpoint
[erigon_data_20230213.tar.lz4](https://download.bsc-snapshot.workers.dev/erigon_data_20230213.tar.lz4
)

### Usage

Step 1: Preparation

- Make sure your hardware meets the [suggested requirement](https://github.com/ledgerwatch/erigon#system-requirements).
- BSC Archive: 7TB. BSC Full: 1TB.

Step 2: Download && Uncompress

```
sudo yum install aria2c
aria2c -s14 -x14 -k100M https://download.bsc-snapshot.workers.dev/{filename} -o erigon.tar.lz4
tar -I lz4 -xvf {filename}
```
Step 3: Replace Data And Restart erigon
- Stop the running erigon client by `kill {pid}`
- Backing up the original data: `mv ${erigon_datadir}/chaindata/mdbx.dat ${erigon_datadir}/mdbx_back.dat`
- Replace the data: `mv ${erigon_snapshot_dir}/erigon/chaindata/mdbx.dat ${erigon_datadir}/chaindata/mdbx.dat`
- Start the erigon client again and check logs

```shell
./build/bin/erigon --chain bsc --db.pagesize=16k --datadir=${erigon_dir/data}
```


