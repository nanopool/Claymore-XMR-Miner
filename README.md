# Claymore-XMR-Miner
Claymore's CryptoNote GPU Miner

This is POOL version.

Catalyst 15.12 is required for best performance and compatibility.

Set "GPU_MAX_ALLOC_PERCENT" environment variable as "100".

For multi-GPU systems, set Virtual Memory size in Windows at least 16 GB:
"Computer Properties / Advanced System Settings / Performance / Advanced / Virtual Memory".

This miner is free-to-use, however, current developer fee is 2% if you use secure SSL/TLS connection to mining pool, miner mines 49 rounds for you and 1 round for developer. 
If you use unsecure connection to mining pool, current developer fee is 2.5%, miner mines 39 rounds for you and 1 round for developer. 
If you don't agree with the dev fee - don't use this miner, or use "-nofee" option.
Attempts to cheat and remove dev fee will cause a bit slower mining speed (same as "-nofee 1") though miner will show same hashrate.

This version is for recent AMD videocards only: 7xxx and 2xx, also 6xxx and 5xxx. No nVidia support.
This version is for Windows x64 only. No 32-bit support. 
Linux version also requires AMD APP SDK installed. Linux version has been tested in Cent 6.5 and Ubuntu 14.04.
Miner has built-in GPU overclocking features and temperature management, also it supports remote monitoring/management.



COMMAND LINE OPTIONS:

-o    pool address. Both HTTP and Stratum protocol are supported. You can specify several "-o" parameters to use several pools, or use "epools.txt" file, or use both. 
   Miner also supports Stratum with SSL/TLS encryption for all data between miner and pool (if pool supports encryption), it significantly improves security.
   To enable encryption, use "ssl://" or "stratum+ssl://" prefix (or "tls" instead of "ssl"), for example: "-o ssl://us-east.cryptonight-hub.miningpoolhub.com:20580"

-u    your wallet address. If your pool requires Username.Worker instead of wallet address, add "-allpools 1" option if miner shows an error.

-p    password, use "x" as password.

-allpools Specify "-allpools 1" if miner does not want to mine on specified pool (because it cannot mine devfee on that pool), but you agree to use some default pools for devfee mining. 
   Note that if devfee mining pools will stop, entire mining will be stopped too.

-di    GPU indexes, default is all available GPUs. For example, if you have four GPUs "-di 02" will enable only first and third GPUs (#0 and #2).
   You can also turn on/off cards in runtime with "0"..."9" keys and check current statistics with "s" key.

-a   Select algorithm:
   "-a 0" (default) means autoselection.
   "-a 1" fastest mining mode for 290/290x and 270/270x cards.
   "-a 2" low power consumption mode for 290/290x cards. A bit less speed (1%) but less power consumption (10-15%).
   "-a 3" fastest mining for old cards (6xxx and 5xxx). 
   "-a 4" fastest mining for recent cards (280x, 270/270x etc). 
   You can also specify mode for every card separately, for example, "-a 1234".
   It is very important for good mining speed to select correct "-a" and "-h" values for your cards.
   For old cards 5xxx and 6xxx use "-a 3" mode only, otherwise you can get "LowDifficultyShare" errors from pool.

-ftime   failover main pool switch time, in minutes, see "Failover" section below. Default value is 30 minutes, set zero if there is no main pool.

-wd    watchdog option. Use "-wd 1" to enable watchdog, miner will be closed if any thread is not responding for 1 minute or OpenCL call failed, 
   so you can restart it from some script if some GPU does not respond.

-ee    close miner if no more pools are available in the list. By default, miner tries all pools one by one, after last pool it tries first pool again and so on. 
Use "-ee 1" to close miner when it tried all pools, so you can restart it from some script and do some additional actions related to internet connectins if necessary.

-retrydelay   delay, in seconds, between connection attempts. Default values is "20". Specify "-retrydelay -1" if you don't need reconnection, in this mode miner will exit if connection is lost.

-dbg   debug log and messages. "-dbg 0" (default) - create log file but don't show debug messages. 
   "-dbg 1" - create log file and show debug messages. "-dbg -1" - no log file and no debug messages.

-r   Restart miner mode. "-r 0" (default) - restart miner if something wrong with GPU. "-r -1" - disable automatic restarting. -r >20 - restart miner if something 
   wrong with GPU or by timer. For example, "-r 60" - restart miner every hour or when some GPU failed.
   "-r 1" closes miner and execute "reboot.bat" file ("reboot.bash" or "reboot.sh" for Linux version) in the miner directory (if exists) if some GPU failed. 
   So you can create "reboot.bat" file and perform some actions, for example, reboot system if you put this line there: "shutdown /r /t 5 /f".

-li   Low intensity mode. Specify "-li 1" if you don't want Windows to freeze.

-nofee: set "1" to cancel my developer fee at all. In this mode some recent optimizations are disabled so mining speed will be slower by about 5%. 
   By enabling this mode, I will lose 100% of my earnings, you will lose only 2.5% of your earnings.
   So you have a choice: "fastest miner" or "completely free miner but a bit slower".
   If you want both "fastest" and "completely free" you should find some other miner that meets your requirements, just don't use this miner instead of claiming that I need 
   to cancel/reduce developer fee, saying that 2.5% developer fee is too much for this miner and so on.

-tt   set target GPU temperature. For example, "-tt 80" means 80C temperature. You can also specify values for every card, for example "-tt 70,80,75".
   You can also set static fan speed if you specify negative values, for example "-tt -50" sets 50% fan speed. Specify zero to disable control and hide GPU statistics.
   "-tt 1" (default) does not manage fans but shows GPU temperature and fan status every 30 seconds. Specify values 2..5 if it is too often.

-tstop   set stop GPU temperature, miner will stop mining if GPU reaches specified temperature. For example, "-tstop 95" means 95C temperature. You can also specify values for every card, for example "-tstop 95,85,90".
   This feature is disabled by default ("-tstop 0"). You also should specify non-zero value for "-tt" option to enable this option.
   NOTE: Check "KNOWN ISSUES" section. GPU indexes in temperature control sometimes don't match GPU indexes in mining!
   If it turned off wrong card, it will close miner in 30 seconds.
   You can also specify negative value to close miner immediately instead of stopping GPU, for example, "-tstop -95" will close miner as soon as any GPU reach 95C temperature.


-h   number of hashes that GPU processes during one round (HashCnt). If not specified, default value is used. For many cases you can increase performance by adjusting this parameter value. 
   For example, "-h 832" means HashCnt=832 for all cards. You can also specify values for every card, for example "-h 640,832,1376,0", zero value means autoselection. 
   Values must be divisible by 32, otherwise near value that is divisible by 32 will be selected.
   Use "m", "+" and "-" keys to find the best value. One hash takes about 2MB of GPU memory, too big value may cause unstable mining and in many cases max value is not the best. 
   For example, for 280x card the best value is 832. For 290x card the best value is 1376.
   For most popular cards miner sets the best value automatically, but there are many cards and I don't have them all, so you can find the best "-h" value by yourself.
   Press "m" key to see current hashrate for every GPU round, then use "+" and "-" keys to increase/reduce "-h" value. You can also press "0"..."9" keys to select active card.
   Sometimes changing "HashCnt" by "+" and "-" keys causes problems with OpenCL, in this case you need to restart miner and change "-h" values in command line directly.

-fanmax   set maximal fan speed, in percents, for example, "-fanmax 80" will set maximal fans speed to 80%. You can also specify values for every card, for example "-fanmax 50,60,70".
   This option works only if miner manages cooling, i.e. when "-tt" option is used to specify target temperature. Default value is "100".

-fanmin   set minimal fan speed, in percents, for example, "-fanmin 50" will set minimal fans speed to 50%. You can also specify values for every card, for example "-fanmin 50,60,70".
   This option works only if miner manages cooling, i.e. when "-tt" option is used to specify target temperature. Default value is "0".

-cclock   set target GPU core clock speed, in MHz. If not specified or zero, miner will not change current clock speed. You can also specify values for every card, for example "-cclock 1000,1050,1100,0".

-mclock   set target GPU memory clock speed, in MHz. If not specified or zero, miner will not change current clock speed. You can also specify values for every card, for example "-mclock 1200,1250,1200,0".

-powlim set power limit, from -50 to 50. If not specified, miner will not change power limit. You can also specify values for every card, for example "-powlim 20,-20,0,10".

-cvddc   set target GPU core voltage, multiplied by 1000. For example, "-cvddc 1050" means 1.05V. You can also specify values for every card, for example "-cvddc 900,950,1000,970". Supports latest AMD 4xx cards only in Windows.

-mvddc   set target GPU memory voltage, multiplied by 1000. For example, "-mvddc 1050" means 1.05V. You can also specify values for every card, for example "-mvddc 900,950,1000,970". Supports latest AMD 4xx cards only in Windows.

-mport   remote monitoring/management port. Default value is -3333 (read-mode), specify "-mport 0" to disable remote monitoring/management feature. 
   Specify negative value to enable monitoring (get statistics) but disable management (restart, uploading files), for example, "-mport -3333" enables port 3333 for remote monitoring, but remote management will be blocked.
   You can also use your web browser to see current miner state, for example, type "localhost:3333" in web browser. 
   Warning: use negative option value or disable remote management entirely if you think that you can be attacked via this port!

-colors enables or disables colored text in console. Default value is "1", use "-colors 0" to disable coloring.

-v   displays miner version, sample usage: "-v 1".



CONFIGURATION FILE

You can use "config.txt" file instead of specifying options in command line. 
If there are not any command line options, miner will check "config.txt" file for options.
If there is only one option in the command line, it must be configuration file name.
If there are two or more options in the command line, miner will take all options from the command line, not from configuration file.
Place one option per line, if first character of a line is ";" or "#", this line will be ignored. 
You can also use environment variables in "epools.txt" and "config.txt" files. For example, define "WORKER" environment variable and use it as "%WORKER%" in config.txt or in epools.txt.



SAMPLE USAGE

 nanopool SSL/TLS connection:
   NsGpuCNMiner.exe -o ssl://xmr-eu1.nanopool.org:14433 -u YourWallet.YourPaymentID.YourWorker/YourEmail -p x

Do not forget to specify your wallet address!



FAILOVER

Use "epools.txt" file to specify additional pools. This file has text format, one pool per line. Every pool has 3 connection attempts. 
Miner disconnects automatically if pool does not send new jobs for a long time or if pool rejects too many shares.
If the first character of a line is ";" or "#", this line will be ignored. 
Do not change spacing, spaces between parameters and values are required for parsing.
If you need to specify "," character in parameter value, use two commas - ,, will be treated as one comma.
You can reload "epools.txt" file in runtime by pressing "r" key.
Pool specified in the command line is "main" pool, miner will try to return to it every 30 minutes if it has to use some different pool from the list. 
If no pool was specified in the command line then first pool in the failover pools list is main pool.
You can change 30 minutes time period to some different value with "-ftime" option, or use "-ftime 0" to disable switching to main pool.
You can also use environment variables in "epools.txt" and "config.txt" files. For example, define "WORKER" environment variable and use it as "%WORKER%" in config.txt or in epools.txt.



PERFORMANCE

About 810 h/s on stock 290X (Hynix memory).
About 710 h/s on stock 290X (Elpida memory).
About 690 h/s on stock 290  (Elpida memory).
About 550 h/s on stock 280X (Hynix memory).
About 440 h/s on stock 270X (Elpida memory).
About 410 h/s on stock 270  (Elpida memory).


TROUBLESHOOTING

1. Install Catalyst v15.12.
2. Disable overclocking.
3. Set GPU_MAX_ALLOC_PERCENT 100.
4. Set Virtual Memory 16 GB.
5. Reboot computer.



TUNING

For 290X/290/280X/270X/270 you can leave default parameters, i.e. do not specify "-a" and "-h", miner will apply best settings automatically.

For other cards, guide how to get maximal speed:

1. Try "-a 1", "-a 3", "-a 4" and select best mode, press "m" key to see immediate hashrate.
2. Remember "HashCnt" value when miner starts, then specify "-h" parameter with this value, remember hashrate and change "-h" parameter step by step by 32 in both directions (less and more). 
   Select best value based on immediate hashrate.



FAQ:

Q: I see too many devfee shares, it looks like devfee is 50%!

A: It's not true, please learn some theory about pool mining.
Some pools use vardiff - they change share target according miner speed. If miner is slow, pool set lower target share, and vice versa. It allows miner to send shares regularly, both fast and slow miners will send similar number of shares per minute, pool will not be overloaded by shares from fast miners and it will not lose slow miners that cannot find valuable shares often. By the way, when you mine solo the share target is block target (very high) so you will find shares very rarely but they will solve blocks and therefore will have maximal value.
Low share target means that miner will find a lot of cheaper shares, high share target means that miner will find fewer shares but they will be more valuable.
When share is found, miner displays share target, for example:

SHARE FOUND (target 10000) 

or 

DevFee: SHARE FOUND (target 1000) 

Compare current targets for main and devfee connections. From example above, 10 devfee shares at target 1000 cost as one share at target 10000. 
But devfee mining is very slow and therefore pool uses x10 lower target. So you see a lot of devfee shares but they are much cheaper than main shares.