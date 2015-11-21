  hlx: HTTP Server Utilities
=========

## What are they
A few utilities for testing and curling from http servers.

## *hurl* HTTP Server Load Tester
*hurl* is an http server load tester similar to ab/siege/weighttp/wrk with support for mulithreading, parallelism, ssl, url ranges, and an api-server for querying the running performance statistics.  *hurl* is primarily useful for benchmarking http server applications.

* **A little more about URLs Ranges**:
*hurl* has support for range expansion in urls which is useful for testing a server's capability to serve from many files. *hurl* will expand the ranges specified in the wildcards and perform requests in user configurable orders (see the "--mode" option in help).
eg: "http://127.0.0.1:8089/[1-100]/my_[1-9]_file.html".

* **Stats API**:
If  *hurl* is started with *-P port_number* option,  *hurl* listens on the user specified port for stats requests:
For example if hurl is run with:
```bash
~>hurl "http://127.0.0.1:8089/index.html" -P12345
Running 1 parallel connections with: 1 reqs/conn, 1 threads
+-----------/-----------+-----------+-----------+--------------+-----------+-------------+-----------+
|    Cmpltd /     Total |    IdlKil |    Errors | kBytes Recvd |   Elapsed |       Req/s |      MB/s |
+-----------/-----------+-----------+-----------+--------------+-----------+-------------+-----------+
|      2603 /      2603 |         0 |         0 |      2158.15 |     0.20s |       0.00s |     0.00s |
|      5250 /      5250 |         0 |         0 |      4352.78 |     0.40s |   13235.00s |    10.72s |
|      7831 /      7831 |         0 |         0 |      6492.69 |     0.60s |   12905.00s |    10.45s |
|     10390 /     10390 |         0 |         0 |      8614.37 |     0.80s |   12795.00s |    10.36s |
|     13131 /     13131 |         0 |         0 |     10886.93 |     1.00s |   13705.00s |    11.10s |
|     15737 /     15737 |         0 |         0 |     13047.57 |     1.20s |   13030.00s |    10.55s |
...
```
*hurl* can be queried:
```bash
~>curl -s http://127.0.0.1:12345 | json_pp
{
   "data" : [
      {
         "value" : {
            "count" : 331578,
            "time" : 1414786046242
         },
         "key" : "http://127.0.0.1:8089/index.html"
      }
   ]
}
```

####An example
```bash
hurl "http://127.0.0.1/index.html" --num_calls=100 -p100 -f100000 -c
```

####Options
```bash
Usage: hurl [http[s]://]hostname[:port]/path [options]
Options are:
  -h, --help          Display this help and exit.
  -r, --version       Display the version number and exit.

Input Options:
  -u, --url_file      URL file.
  -w, --no_wildcards  Don't wildcard the url.
  -d, --data          HTTP body data -supports bodies up to 8k.

Settings:
  -y, --cipher        Cipher --see "openssl ciphers" for list.
  -p, --parallel      Num parallel Default: 64.
  -f, --fetches       Num fetches.
  -N, --num_calls     Number of requests per connection
  -k, --keep_alive    Re-use connections for all requests
  -t, --threads       Number of parallel threads.
  -H, --header        Request headers -can add multiple ie -H<> -H<>...
  -X, --verb          Request command -HTTP verb to use -GET/PUT/etc
  -l, --seconds       Run for <N> seconds .
  -A, --rate          Max Request Rate.
  -M, --mode          Requests mode [roundrobin|sequential|random].
  -R, --recv_buffer   Socket receive buffer size.
  -S, --send_buffer   Socket send buffer size.
  -D, --no_delay      Socket TCP no-delay.
  -T, --timeout       Timeout (seconds).

Print Options:
  -v, --verbose       Verbose logging
  -c, --color         Color
  -q, --quiet         Suppress progress output
  -C, --responses     Display http(s) response codes instead of request statistics
  -L, --responses_per Display http(s) response codes per interval instead of request statistics

Stat Options:
  -P, --data_port     Start HTTP Stats Daemon on port.
  -B, --breakdown     Show breakdown
  -Y, --http_load     Display in http load mode [MODE] -Legacy support
  -G, --gprofile      Google profiler output file

Note: If running long jobs consider enabling tcp_tw_reuse -eg:
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
```
## *phurl* Parallel Curl
*phurl* is a parallel curling utility useful for pulling a single url from many different hosts. *phurl* supports reading line delimited hosts from stdin, a shell command string, or a file.

####An example
```bash
printf "www.google.com\nwww.yahoo.com\nwww.reddit.com\n" | phurl -p2 -t3 -u"https://bloop.com/" -s -c -T5
```

####Options
```bash
Usage: phurl -u [http[s]://]hostname[:port]/path [options]
Options are:
  -h, --help           Display this help and exit.
  -r, --version        Display the version number and exit.

URL Options -or without parameter
  -u, --url            URL -REQUIRED (unless running cli: see --cli option).
  -d, --data           HTTP body data -supports bodies up to 8k.

Hostname Input Options -also STDIN:
  -f, --host_file      Host name file.
  -x, --execute        Script to execute to get host names.

Settings:
  -p, --parallel       Num parallel.
  -t, --threads        Number of parallel threads.
  -H, --header         Request headers -can add multiple ie -H<> -H<>...
  -X, --verb           Request command -HTTP verb to use -GET/PUT/etc
  -T, --timeout        Timeout (seconds).
  -R, --recv_buffer    Socket receive buffer size.
  -S, --send_buffer    Socket send buffer size.
  -D, --no_delay       Socket TCP no-delay.
  -A, --ai_cache       Path to Address Info Cache (DNS lookup cache).
  -C, --connect_only   Only connect -do not send request.

SSL Settings:
  -y, --cipher         Cipher --see "openssl ciphers" for list.
  -O, --ssl_options    SSL Options string.
  -V, --ssl_verify     Verify server certificate.
  -N, --ssl_sni        Use SSL SNI.
  -F, --ssl_ca_file    SSL CA File.
  -L, --ssl_ca_path    SSL CA Path.

Command Line Client:
  -I, --cli            Start interactive command line -URL not required.

Print Options:
  -v, --verbose        Verbose logging
  -c, --color          Color
  -q, --quiet          Suppress output
  -s, --show_progress  Show progress
  -m, --show_summary   Show summary output

Output Options: -defaults to line delimited
  -o, --output         File to write output to. Defaults to stdout
  -l, --line_delimited Output <HOST> <RESPONSE BODY> per line
  -j, --json           JSON { <HOST>: "body": <RESPONSE> ...
  -P, --pretty         Pretty output


Note: If running large jobs consider enabling tcp_tw_reuse -eg:
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
```

## *hlxcore* hlx HTTP API Client/Server Library
*hlxcore* is a library for creating simple simple API servers with support for subrequests.

####An example
```cpp
#include <hlx/hlx.h>
#include <string.h>

class bananas_getter: public ns_hlx::default_rqst_h
{
public:
        // GET
        ns_hlx::h_resp_t do_get(ns_hlx::hlx &a_hlx,
                                ns_hlx::hconn &a_hconn,
                                ns_hlx::rqst &a_rqst,
                                const ns_hlx::url_pmap_t &a_url_pmap)
        {
                char l_len_str[64];
                uint32_t l_body_len = strlen("Hello World\n");
                sprintf(l_len_str, "%u", l_body_len);
                ns_hlx::api_resp &l_api_resp = a_hlx.create_api_resp();
                l_api_resp.set_status(ns_hlx::HTTP_STATUS_OK);
                l_api_resp.set_header("Content-Length", l_len_str);
                l_api_resp.set_body_data("Hello World\n", l_body_len);
                a_hlx.queue_api_resp(a_hconn, l_api_resp);
                return ns_hlx::H_RESP_DONE;
        }
};

int main(void)
{
        ns_hlx::lsnr *l_lsnr = new ns_hlx::lsnr(12345, ns_hlx::SCHEME_TCP);
        ns_hlx::rqst_h *l_rqst_h = new bananas_getter();
        l_lsnr->add_endpoint("/bananas", l_rqst_h);
        ns_hlx::hlx *l_hlx = new ns_hlx::hlx();
        l_hlx->add_lsnr(l_lsnr);
        // Run in foreground w/ threads == 0
        l_hlx->set_num_threads(0);
        l_hlx->run();
        return 0;
}
```
#####Build with:
```bash
g++ basic.cc -lhlxcore -lssl -lcrypto -lpthread -o basic
```
#####Running:
```bash
./hlx_server_ex &
curl "127.0.0.1:12345/bananas"
Hello World
```

## Building

##OS requirements:
Linux for now (epoll only -kqueue support coming soon-ish)

###Install dependecies:
Library requirements:
* libssl/libcrypto (OpenSSL)
* libgoogle-perftools

###Building the tools
```bash
./build.sh
```

And optionally install
```bash
cd ./build
sudo make install
```
