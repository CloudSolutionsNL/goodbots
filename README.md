# goodbots - trust but verify
goodbots verifies the IP addresses of respectful crawlers like Googlebot by performing [reverse dns](https://searchsignals.com/how-to-do-a-reverse-dns-lookup) and forward dns 
lookups.

1. Given an IP address (ex. `66.249.87.225`)
2. It performs a reverse dns lookup to get a hostname (ex. `crawl-203-208-60-1.googlebot.com`) 
3. Then does a forward dns lookup on the hostname to get an IP (ex. `66.249.87.225`) 
4. It compares the 1st IP to the 2nd IP
5. If they match, goodbots outputs the IP and hostname 

# Getting Started
***
##  How to install/build goodbots
Clone the repo:

```git clone git@github.com:eywu/goodbots.git```

Change to the `/cmd/goodbots` directory:

```cd goodbots/cmd/goodbots```

Build the binary/executable `main.go` file:

```go build```

## How to use goodbots
If you've built the `main.go` file that comes with goodbots above, you can simply feed goodbots IPs via standard-in. 

Test a single IP

```echo "203.208.60.1" | ./goodbots```

Test a range of IPs

```prips 203.208.40.1 203.208.80.1 | ./goodbots```

Test a list of IPs from a text or csv file

```./goodbots < ip-list.txt```

__note:__ The CSV or text file expects only an IP on its own line.

Example:
```
66.249.87.224
203.208.23.146
203.208.23.126
203.208.60.227
```

### Saving the results 

goodbots prints to standard out with tab delimiters, so you can capture the output with an [output redirect]
(https://www.codecademy.com/learn/learn-the-command-line/modules/learn-the-command-line-redirection/cheatsheet). 

Saving verified bot IPs and hosts to a filed named `saved-results.tsv`
```./goodbots < ip-list.txt > saved-results.tsv```

## DNS Resolvers
goodbots randomly selects a different public DNS resolver for each DNS lookup to reduce the chances of being blocked or 
throttled by your DNS provider if you have lots of IPs to verify. 

It uses these DNS providers:
* [CloudFlare Public DNS](https://www.cloudflare.com/learning/dns/what-is-1.1.1.1/) 
  * 1.1.1.1
  * 1.0.0.1
* [Google Public DNS](https://developers.google.com/speed/public-dns)
  * 8.8.8.8
  * 8.8.4.4
* [Open DNS](https://www.opendns.com/setupguide/)
  * 208.67.222.222
  * 208.67.220.220
* [Quad9 DNS](https://www.quad9.net/) (⛔ _not supported yet_)
  * ~~9.9.9.9~~
  * ~~149.112.112.112~~

## Supported Crawlers
Currently verifying the domain name is a little imprecise. goodbots looks for just the domain name to match and does 
__not__ match the TLD.

Future improvements will test for more precise domains based on the crawlers specifications.

* googlebot
  * .googlebot.
  * .google.
* msnbot
  * .msn.
* bingbot
  * .msn.
* pinterest
  * .pinterest.
* yandex
  * .yandex.
* baidu
  * .baidu.
* coccoc
  * .coccoc.
  
## Make it go faster!
By default we only set the concurrency of requests to 10. If you want to speed up the work, you can increase that 
number by modifying the `main.go` file before building the binary/executable.

# Other usage of goodbots
In building goodbots, we created a general purpose function for simply resolving the hostnames of any IP address. 

In `main.go` you can uncomment the line that calls `ResolveNames()` and comment out the `GoodBots()` function call.

This will not perform a forward DNS lookup to verify the hostname resolves to the same IP address. Additionally, it 
will output errors to the TSV output when it encounters IPs that error out when requesting the hostname.
```
➜  goodbots git:(main) ✗ prips -i 50 66.100.0.0 66.200.0.0 | ./goodbots
66.100.0.50	(error)	lookup 50.0.100.66.in-addr.arpa. on 192.168.1.1:53: no such host
...
66.100.1.144	(error)	lookup 144.1.100.66.in-addr.arpa. on 192.168.1.1:53: no such host
66.100.0.150	WebGods
66.100.0.250	(error)	lookup 250.0.100.66.in-addr.arpa. on 192.168.1.1:53: no such host
...
66.100.4.76	(error)	lookup 76.4.100.66.in-addr.arpa. on 192.168.1.1:53: no such host
66.100.4.126	mail.esai.com
```
***
Written in [Golang](https://golang.org/)
![gopher](https://user-images.githubusercontent.com/185250/118390633-f73be280-b5e4-11eb-8f60-bba0abb2f119.png)
Gopher courtesy of [Gopherize.me](https://gopherize.me/)
