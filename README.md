#OpenWrt main config
rule-providers:
  ZEROTIER:
    type: file
    behavior: classical
    path: "./rule_provider/zerotier.yaml" 
  BANK:
    type: file
    behavior: classical
    path: "./rule_provider/BANK.YAML" 
  adaway:
    type: file
    behavior: classical
    path: "./rule_provider/adaway.yaml"
  adguard_dns:
    type: file
    behavior: classical
    path: "./rule_provider/AdguardDNS.yaml"
  abpindo:
    type: file
    behavior: classical
    path: "./rule_provider/ABPindo.yaml"

# ================= Mukelist==================
proxy-providers:
  PROXY_UTAMA:
    type: file
    path: "./proxy_provider/PROXY_UTAMA.yaml"
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
  PROXY_CADANGAN:
    type: file
    path: "./proxy_provider/PROXY_CADANGAN.yaml"
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
  
# ================= Mukelist ==================
proxy-groups:
- name: ZEROTIER
  type: url-test
  use:
  - PROXY_CADANGAN
  url: http://www.gstatic.com/generate_204
  interval: '300'
- name: PROXY_UTAMA
  type: select 
  use:
  - PROXY_UTAMA
  url: http://www.gstatic.com/generate_204
  interval: '300'
- name: PROXY_CADANGAN
  type: select
  use:
  - PROXY_CADANGAN
  url: http://www.gstatic.com/generate_204
  interval: '300'
- name: ALL_PROXIES
  type: url-test
  use:
  - PROXY_UTAMA
  - PROXY_CADANGAN
  url: http://www.gstatic.com/generate_204
  interval: '300'
# ================= Mukelist-STB ==================
dns:
  nameserver:
    - https://dns-google.com/dns-query
    - https://unfiltered.adguard-dns.com/dns-query
    - 8.8.8.8
  default-nameserver:
    - 8.8.8.8
  enable: true
  ipv6: false
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  listen: 127.0.0.1:7874
  fake-ip-filter:
  - "+.*"
  redir-port: 7892
  tproxy-port: 7895
  port: 7890
  socks-port: 7891
  mixed-port: 7893
  mode: global
  log-level: silent
  allow-lan: true
  external-controller: 0.0.0.0:9090
  secret: 123456
  bind-address: "*"
  external-ui: "/usr/share/openclash/ui"
ipv6: false
tun:
  enable: true
  stack: system
  dns-hijack:
  - tcp://any:53
profile:
  store-selected: true
  store-fake-ip: true
rules:
- RULE-SET,adaway,REJECT
- RULE-SET,adguard_dns,REJECT
- RULE-SET,abpindo,REJECT
- RULE-SET,BANK,DIRECT
- RULE-SET,ZEROTIER,ZEROTIER
- IP-CIDR,198.18.0.1/16,REJECT,no-resolve
- MATCH,GLOBAL
experimental:
  sniff-tls-sni: true
