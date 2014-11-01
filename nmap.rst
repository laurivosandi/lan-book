.. title: nmap
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. license: cc-by-3
.. tags: nmap, security
.. date: 2014-04-17

*Network Mapper* on turvaskanner mida kasutatakse masinate ning nendes
käitatavate teenuste avastamiseks võrgus.

Kõige lihtsam ning ka kõige kiirem
kasutusjuht käib läbi alamvõrgu
IP aadressid ning *ping*-ib igaühte:

.. code::

    lauri-e450 ~ # nmap -n -sP 192.168.3.0/24

    Starting Nmap 5.21 ( http://nmap.org ) at 2013-08-23 15:37 EEST
    Nmap scan report for 192.168.3.1
    Host is up (0.00044s latency).
    Nmap scan report for 192.168.3.2
    Host is up (0.00032s latency).
    Nmap scan report for 192.168.3.100
    Host is up (0.00071s latency).
    Nmap scan report for 192.168.3.170
    Host is up (0.030s latency).
    Nmap done: 256 IP addresses (4 hosts up) scanned in 2.51 seconds

Eemaldades *-n* võtme tehakse ka tagurpidi nimelahendust:

.. code::

    lauri-e450 ~ # nmap -sP 192.168.3.0/24

    Starting Nmap 5.21 ( http://nmap.org ) at 2013-08-23 15:40 EEST
    Nmap scan report for tplink-wdr4300.lan (192.168.3.1)
    Host is up (0.00026s latency).
    MAC Address: 90:F6:52:F5:FC:C6 (Unknown)
    Nmap scan report for lauri-e450.lan (192.168.3.2)
    Host is up.
    Nmap scan report for 192.168.3.100
    Host is up (0.00013s latency).
    MAC Address: 00:08:9B:C9:1F:42 (ICP Electronics)
    Nmap scan report for Timbulimbu-PC.lan (192.168.3.170)
    Host is up (0.019s latency).
    MAC Address: 1C:65:9D:48:CF:42 (Unknown)
    Nmap done: 256 IP addresses (4 hosts up) scanned in 4.91 seconds

Küsides iga IP-aadressi kohta tagurpidi nimelahendust võib võrgust tuvastada
veelgi mõnda huvitavat, näiteks masinad mis on olnud võrgus veel mõnda
aega tagasi:

.. code::

    lauri-e450 ~ # nmap -R -sP 192.168.3.0/24

    Starting Nmap 5.21 ( http://nmap.org ) at 2013-08-23 15:43 EEST
    Nmap scan report for 192.168.3.0 [host down]
    Nmap scan report for tplink-wdr4300.lan (192.168.3.1)
    Host is up (0.00025s latency).
    MAC Address: 90:F6:52:F5:FC:C6 (Unknown)
    Nmap scan report for lauri-e450.lan (192.168.3.2)
    Host is up.
    Nmap scan report for 192.168.3.3 [host down]
    Nmap scan report for 192.168.3.4 [host down]
    Nmap scan report for 192.168.3.5 [host down]
    Nmap scan report for 192.168.3.6 [host down]
    Nmap scan report for 192.168.3.7 [host down]
    Nmap scan report for 192.168.3.8 [host down]
    Nmap scan report for 192.168.3.9 [host down]
    Nmap scan report for 192.168.3.10 [host down]
    Nmap scan report for 192.168.3.11 [host down]
    Nmap scan report for 192.168.3.12 [host down]
    Nmap scan report for 192.168.3.13 [host down]
    Nmap scan report for 192.168.3.14 [host down]
    Nmap scan report for 192.168.3.15 [host down]
    Nmap scan report for 192.168.3.16 [host down]
    Nmap scan report for 192.168.3.17 [host down]
    Nmap scan report for 192.168.3.18 [host down]
    Nmap scan report for 192.168.3.19 [host down]
    Nmap scan report for 192.168.3.20 [host down]
    Nmap scan report for 192.168.3.21 [host down]
    Nmap scan report for 192.168.3.22 [host down]
    Nmap scan report for 192.168.3.23 [host down]
    Nmap scan report for 192.168.3.24 [host down]
    Nmap scan report for 192.168.3.25 [host down]
    Nmap scan report for 192.168.3.26 [host down]
    Nmap scan report for 192.168.3.27 [host down]
    Nmap scan report for 192.168.3.28 [host down]
    Nmap scan report for 192.168.3.29 [host down]
    Nmap scan report for 192.168.3.30 [host down]
    Nmap scan report for 192.168.3.31 [host down]
    Nmap scan report for 192.168.3.32 [host down]
    Nmap scan report for 192.168.3.33 [host down]
    Nmap scan report for 192.168.3.34 [host down]
    Nmap scan report for 192.168.3.35 [host down]
    Nmap scan report for 192.168.3.36 [host down]
    Nmap scan report for 192.168.3.37 [host down]
    Nmap scan report for 192.168.3.38 [host down]
    Nmap scan report for 192.168.3.39 [host down]
    Nmap scan report for 192.168.3.40 [host down]
    Nmap scan report for 192.168.3.41 [host down]
    Nmap scan report for 192.168.3.42 [host down]
    Nmap scan report for 192.168.3.43 [host down]
    Nmap scan report for 192.168.3.44 [host down]
    Nmap scan report for 192.168.3.45 [host down]
    Nmap scan report for 192.168.3.46 [host down]
    Nmap scan report for 192.168.3.47 [host down]
    Nmap scan report for 192.168.3.48 [host down]
    Nmap scan report for 192.168.3.49 [host down]
    Nmap scan report for 192.168.3.50 [host down]
    Nmap scan report for 192.168.3.51 [host down]
    Nmap scan report for 192.168.3.52 [host down]
    Nmap scan report for 192.168.3.53 [host down]
    Nmap scan report for 192.168.3.54 [host down]
    Nmap scan report for 192.168.3.55 [host down]
    Nmap scan report for 192.168.3.56 [host down]
    Nmap scan report for 192.168.3.57 [host down]
    Nmap scan report for 192.168.3.58 [host down]
    Nmap scan report for 192.168.3.59 [host down]
    Nmap scan report for 192.168.3.60 [host down]
    Nmap scan report for 192.168.3.61 [host down]
    Nmap scan report for 192.168.3.62 [host down]
    Nmap scan report for 192.168.3.63 [host down]
    Nmap scan report for 192.168.3.64 [host down]
    Nmap scan report for 192.168.3.65 [host down]
    Nmap scan report for 192.168.3.66 [host down]
    Nmap scan report for 192.168.3.67 [host down]
    Nmap scan report for 192.168.3.68 [host down]
    Nmap scan report for 192.168.3.69 [host down]
    Nmap scan report for 192.168.3.70 [host down]
    Nmap scan report for 192.168.3.71 [host down]
    Nmap scan report for 192.168.3.72 [host down]
    Nmap scan report for 192.168.3.73 [host down]
    Nmap scan report for 192.168.3.74 [host down]
    Nmap scan report for 192.168.3.75 [host down]
    Nmap scan report for 192.168.3.76 [host down]
    Nmap scan report for 192.168.3.77 [host down]
    Nmap scan report for 192.168.3.78 [host down]
    Nmap scan report for 192.168.3.79 [host down]
    Nmap scan report for 192.168.3.80 [host down]
    Nmap scan report for 192.168.3.81 [host down]
    Nmap scan report for 192.168.3.82 [host down]
    Nmap scan report for 192.168.3.83 [host down]
    Nmap scan report for 192.168.3.84 [host down]
    Nmap scan report for 192.168.3.85 [host down]
    Nmap scan report for 192.168.3.86 [host down]
    Nmap scan report for 192.168.3.87 [host down]
    Nmap scan report for 192.168.3.88 [host down]
    Nmap scan report for 192.168.3.89 [host down]
    Nmap scan report for 192.168.3.90 [host down]
    Nmap scan report for 192.168.3.91 [host down]
    Nmap scan report for 192.168.3.92 [host down]
    Nmap scan report for 192.168.3.93 [host down]
    Nmap scan report for 192.168.3.94 [host down]
    Nmap scan report for 192.168.3.95 [host down]
    Nmap scan report for 192.168.3.96 [host down]
    Nmap scan report for 192.168.3.97 [host down]
    Nmap scan report for 192.168.3.98 [host down]
    Nmap scan report for 192.168.3.99 [host down]
    Nmap scan report for 192.168.3.100
    Host is up (0.00024s latency).
    MAC Address: 00:08:9B:C9:1F:42 (ICP Electronics)
    Nmap scan report for 192.168.3.101 [host down]
    Nmap scan report for 192.168.3.102 [host down]
    Nmap scan report for 192.168.3.103 [host down]
    Nmap scan report for 192.168.3.104 [host down]
    Nmap scan report for 192.168.3.105 [host down]
    Nmap scan report for 192.168.3.106 [host down]
    Nmap scan report for 192.168.3.107 [host down]
    Nmap scan report for 192.168.3.108 [host down]
    Nmap scan report for 192.168.3.109 [host down]
    Nmap scan report for 192.168.3.110 [host down]
    Nmap scan report for 192.168.3.111 [host down]
    Nmap scan report for 192.168.3.112 [host down]
    Nmap scan report for 192.168.3.113 [host down]
    Nmap scan report for 192.168.3.114 [host down]
    Nmap scan report for 192.168.3.115 [host down]
    Nmap scan report for 192.168.3.116 [host down]
    Nmap scan report for 192.168.3.117 [host down]
    Nmap scan report for 192.168.3.118 [host down]
    Nmap scan report for 192.168.3.119 [host down]
    Nmap scan report for 192.168.3.120 [host down]
    Nmap scan report for 192.168.3.121 [host down]
    Nmap scan report for 192.168.3.122 [host down]
    Nmap scan report for 192.168.3.123 [host down]
    Nmap scan report for 192.168.3.124 [host down]
    Nmap scan report for 192.168.3.125 [host down]
    Nmap scan report for 192.168.3.126 [host down]
    Nmap scan report for 192.168.3.127 [host down]
    Nmap scan report for 192.168.3.128 [host down]
    Nmap scan report for 192.168.3.129 [host down]
    Nmap scan report for 192.168.3.130 [host down]
    Nmap scan report for 192.168.3.131 [host down]
    Nmap scan report for 192.168.3.132 [host down]
    Nmap scan report for 192.168.3.133 [host down]
    Nmap scan report for 192.168.3.134 [host down]
    Nmap scan report for 192.168.3.135 [host down]
    Nmap scan report for 192.168.3.136 [host down]
    Nmap scan report for 192.168.3.137 [host down]
    Nmap scan report for 192.168.3.138 [host down]
    Nmap scan report for 192.168.3.139 [host down]
    Nmap scan report for scylla.lan (192.168.3.140) [host down]
    Nmap scan report for 192.168.3.141 [host down]
    Nmap scan report for 192.168.3.142 [host down]
    Nmap scan report for 192.168.3.143 [host down]
    Nmap scan report for 192.168.3.144 [host down]
    Nmap scan report for 192.168.3.145 [host down]
    Nmap scan report for 192.168.3.146 [host down]
    Nmap scan report for 192.168.3.147 [host down]
    Nmap scan report for 192.168.3.148 [host down]
    Nmap scan report for 192.168.3.149 [host down]
    Nmap scan report for 192.168.3.150 [host down]
    Nmap scan report for android-f1388c61d2b9edbb.lan (192.168.3.151) [host down]
    Nmap scan report for 192.168.3.152 [host down]
    Nmap scan report for 192.168.3.153 [host down]
    Nmap scan report for 192.168.3.154 [host down]
    Nmap scan report for 192.168.3.155 [host down]
    Nmap scan report for 192.168.3.156 [host down]
    Nmap scan report for 192.168.3.157 [host down]
    Nmap scan report for 192.168.3.158 [host down]
    Nmap scan report for 192.168.3.159 [host down]
    Nmap scan report for 192.168.3.160 [host down]
    Nmap scan report for 192.168.3.161 [host down]
    Nmap scan report for 192.168.3.162 [host down]
    Nmap scan report for 192.168.3.163 [host down]
    Nmap scan report for 192.168.3.164 [host down]
    Nmap scan report for 192.168.3.165 [host down]
    Nmap scan report for 192.168.3.166 [host down]
    Nmap scan report for 192.168.3.167 [host down]
    Nmap scan report for 192.168.3.168 [host down]
    Nmap scan report for 192.168.3.169 [host down]
    Nmap scan report for Timbulimbu-PC.lan (192.168.3.170)
    Host is up (0.0059s latency).
    MAC Address: 1C:65:9D:48:CF:42 (Unknown)
    Nmap scan report for 192.168.3.171 [host down]
    Nmap scan report for 192.168.3.172 [host down]
    Nmap scan report for 192.168.3.173 [host down]
    Nmap scan report for 192.168.3.174 [host down]
    Nmap scan report for 192.168.3.175 [host down]
    Nmap scan report for 192.168.3.176 [host down]
    Nmap scan report for 192.168.3.177 [host down]
    Nmap scan report for 192.168.3.178 [host down]
    Nmap scan report for 192.168.3.179 [host down]
    Nmap scan report for android-81f58c56153ca09a.lan (192.168.3.180) [host down]
    Nmap scan report for 192.168.3.181 [host down]
    Nmap scan report for 192.168.3.182 [host down]
    Nmap scan report for 192.168.3.183 [host down]
    Nmap scan report for 192.168.3.184 [host down]
    Nmap scan report for 192.168.3.185 [host down]
    Nmap scan report for 192.168.3.186 [host down]
    Nmap scan report for 192.168.3.187 [host down]
    Nmap scan report for 192.168.3.188 [host down]
    Nmap scan report for 192.168.3.189 [host down]
    Nmap scan report for 192.168.3.190 [host down]
    Nmap scan report for 192.168.3.191 [host down]
    Nmap scan report for 192.168.3.192 [host down]
    Nmap scan report for 192.168.3.193 [host down]
    Nmap scan report for 192.168.3.194 [host down]
    Nmap scan report for 192.168.3.195 [host down]
    Nmap scan report for 192.168.3.196 [host down]
    Nmap scan report for 192.168.3.197 [host down]
    Nmap scan report for 192.168.3.198 [host down]
    Nmap scan report for 192.168.3.199 [host down]
    Nmap scan report for 192.168.3.200 [host down]
    Nmap scan report for 192.168.3.201 [host down]
    Nmap scan report for 192.168.3.202 [host down]
    Nmap scan report for 192.168.3.203 [host down]
    Nmap scan report for 192.168.3.204 [host down]
    Nmap scan report for 192.168.3.205 [host down]
    Nmap scan report for 192.168.3.206 [host down]
    Nmap scan report for 192.168.3.207 [host down]
    Nmap scan report for 192.168.3.208 [host down]
    Nmap scan report for 192.168.3.209 [host down]
    Nmap scan report for 192.168.3.210 [host down]
    Nmap scan report for 192.168.3.211 [host down]
    Nmap scan report for 192.168.3.212 [host down]
    Nmap scan report for 192.168.3.213 [host down]
    Nmap scan report for 192.168.3.214 [host down]
    Nmap scan report for 192.168.3.215 [host down]
    Nmap scan report for 192.168.3.216 [host down]
    Nmap scan report for 192.168.3.217 [host down]
    Nmap scan report for 192.168.3.218 [host down]
    Nmap scan report for 192.168.3.219 [host down]
    Nmap scan report for 192.168.3.220 [host down]
    Nmap scan report for 192.168.3.221 [host down]
    Nmap scan report for 192.168.3.222 [host down]
    Nmap scan report for Jaanus-PC.lan (192.168.3.223) [host down]
    Nmap scan report for 192.168.3.224 [host down]
    Nmap scan report for 192.168.3.225 [host down]
    Nmap scan report for 192.168.3.226 [host down]
    Nmap scan report for 192.168.3.227 [host down]
    Nmap scan report for 192.168.3.228 [host down]
    Nmap scan report for 192.168.3.229 [host down]
    Nmap scan report for 192.168.3.230 [host down]
    Nmap scan report for 192.168.3.231 [host down]
    Nmap scan report for 192.168.3.232 [host down]
    Nmap scan report for 192.168.3.233 [host down]
    Nmap scan report for 192.168.3.234 [host down]
    Nmap scan report for 192.168.3.235 [host down]
    Nmap scan report for 192.168.3.236 [host down]
    Nmap scan report for 192.168.3.237 [host down]
    Nmap scan report for 192.168.3.238 [host down]
    Nmap scan report for 192.168.3.239 [host down]
    Nmap scan report for 192.168.3.240 [host down]
    Nmap scan report for 192.168.3.241 [host down]
    Nmap scan report for 192.168.3.242 [host down]
    Nmap scan report for 192.168.3.243 [host down]
    Nmap scan report for 192.168.3.244 [host down]
    Nmap scan report for 192.168.3.245 [host down]
    Nmap scan report for lauri-t420.lan (192.168.3.246) [host down]
    Nmap scan report for 192.168.3.247 [host down]
    Nmap scan report for 192.168.3.248 [host down]
    Nmap scan report for 192.168.3.249 [host down]
    Nmap scan report for 192.168.3.250 [host down]
    Nmap scan report for 192.168.3.251 [host down]
    Nmap scan report for 192.168.3.252 [host down]
    Nmap scan report for 192.168.3.253 [host down]
    Nmap scan report for 192.168.3.254 [host down]
    Nmap scan report for 192.168.3.255 [host down]
    Nmap done: 256 IP addresses (4 hosts up) scanned in 6.14 seconds

