#  [FluxEnder][4]

Malicious domains refers to websites that spread virus, worms and Trojan horses. These sites are also usually related to fishing and pornography. These sites have been a serious threat to Internet user’s information and property safety. With the utilization of Fast-Flux and DGA technology, these sites turns to be more difficult to be tracked and taken down.

To identify these malicious domains, this paper proposes a machine learning classifier based on random forests algorithm. I call it [FluxEnder][4].

We propose a storage model based on TokuMX to store Passive DNS data. This model have high scalability, query speed and space utilization. It is optimized for analysis and it also supports reverse query on IP address. We build a malicious domain classifier from five aspects: diversity, time, growth, relatedness and IP info, consisting of 18 features. 7 of them are first time to be proposed by this paper. This paper bring up an idea called domain pool which describe the relation between domains. If several domains point to the same IP, they are in the same domain pool. Therefor, we can identify these domains with their group behavior. A malicious domain might use a malicious IP. In our learning model, we also use IP information, which is the info to judge the reliability of an IP. In addition, the model also take the DGA domain into consideration.


-----
## Setup

1. download [dns log][3] from [here][2], these are input dns logs, remember update the 'dns\_log\_path' in log\_to\_mongo.py. you might have your own dns logs, that's ok, you can write your own extraction code.
2. I suggest you update malicious blocklist and cdn domains lists in the /resources folder to make sure you have the latest legal and malware domains. These are supposed for noise deduction. The scripts in /scripts will help you in this part.
3.  store the raw logs into TukuMX or mongoDB with /src/log\_to\_mongo.py and /src/log\_to\_mongo\_lib.py . you can run 'python log\_to\_mongo.py args1 args2', but I wrote a file called worker.py, this is a main entrance for both log to mongo and extraction, just two parts, comment the part you do not want to run.
4.  extract features from mongoDB rows(part 3), in this part, it's easy to come up with something new and help your paper. read the /src/extract\_feature.py and /src/extract\_feature\_lib.py, you can get core ideas of FluxEnder in this two files. the I store the extracted features.
5. this is about final classification, with the /src/classify\_domain.py, I use random forests tree from scikit-learn(thanks for the developers of these fancy tools), I train the model with labeled data, and then classify the test domain, calculate the precision. you can read [dga hacking][5] here, it will help you understand. 

## Tuning

* The malicious domains used(!!)
* CDN names you use(!!)
* Thresholds in classify_domain.py(!!!)

---
##DIRs

**output**
generated by /src/classify_domain.py

* fn.txt: flase negative domains
* fp.txt: false positive domains

**resources**
white\black\CDN domain lists

* cdn.txt: top 50 cdn domains in this planet(manually generated)
* cdn_ip.txt: IPs associated with cdn domains above
* cdn_new.txt: domains which use ips in cdn_ip.txt (cdn_ip.txt and cdn_new.txt are generated by /scripts/find_cdn_user.py)
* domain_whitelist.txt: domains that are legal, which means they might be classified as malicious(manually generated)
* hosts_*: different kinds of malicious domains(generated by /scripts/update_malware_files.py and /scripts/update_malware_files.sh)
* top-1m.csv: Alexa top 1m sites(downlaod from Alexa)

**scripts**
* drop.py: drop collections or dbs from mongo
* find_cdn_cname.py: CDN domains my use CNAME recours recursively, this script can find all the domain names recursively
* find_cdn_user.py: find domains that use CDN IP
* plotting.py: plot various graphs to show the difference between legal and malicious on features
* printting.py: ancient code, for feature value show, work well with interruption
* test.py: tests as it is...
* update_malware_files.*: malware blocklist are frequently updated, so update them

**src**
* models: dga detection related, (check [here][1])
* \__init__.py: kinds of global variables
* classify_domain.py: run on labeled domains and features extracted to check whether the model works, this is the last step
* dga_model_eval.py: dga detection related, (check [here][1])
* extract_feature.py: extract features from raw data, slow
* extract_feature_lib.py: imported by extract_feature.py
* log_to_mongo.py: read log file, store into mongo
* log_to_mongo_lib.py: imported by log_to_mongo.py
* worker.py: the 'main()' for log_to_mongo and extract_feature (python, glue language)

----

Good luck, :)
in case you need help, **chenkainp@gmail.com**


[1]: https://github.com/whodewho/FluxEnder
[2]: http://pan.baidu.com/s/11sYFs
[3]: https://github.com/gamelinux/passivedns
[4]: http://kthinker.com/2014/06/01/detection-of-malicious-domains-based-on-passive-dns-analysis.html
[5]: https://github.com/ClickSecurity/data_hacking/tree/master/dga_detection



