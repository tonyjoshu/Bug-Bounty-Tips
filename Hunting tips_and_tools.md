## How to approach the target
- Get the domain/domains from the HacherOne Scope
## Findind Subdomains  
### Tips
- Finding subdomains from the domains you gathered.
 #### Tools <br>
- ``subfinder``
- ``gobuster``
- ``ffuf``
- ``crt.sh``
  ### Example: <br>
  #### Using subfinder
  ```sh
  subfinder -dL domains.txt -all -recursive -o subdomains.txt
  ```
    #### Using ``gobuster`` (limited to one domain)
  ```sh
  gobuster dns -u "example.com" -w /your/wordlist/path -t 100
  ``` 
    #### Using ``ffuf``
  ```sh
  ffuf -u http://example.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -fw 18 -t 100 -H "HOST: FUZZ.example.com"
  ```
    #### Using ``crt.sh`` (with command line)
  ```sh
  curl -s https://crt.sh/\?q\=example.com\&output\=json | jq -r '.[] | select(.name_value != null) | .name_value' | grep -Po '(\w+\.\w+\.\w+)$' > examplesubdomains.txt
  ```
    #### Using ``httpx-toolkit``(Checking for alive subdomains)
  ```sh
  cat examplesubdomains.txt| httpx-toolkit -l amazonsub.txt -ports 443,80,8080,8000,8888 -threads 200 > examplesubdomains_alive.txt
  ```
    #### Using ``naabu`` (Checking for alive subdomains and port scanning)
  ```sh
  naabu -list amazonsub.txt -c 50 -nmap-cli 'nmap -sV -sC' -o naabu-file.txt
  ```
  ### Directory enumirattion
  #### Tools
  - ``gobuster``
  - ``feroxbuster``
  - ``dirb``
  - ``dirbuster``
  - ``dirsearch`` (excellent)
    ### Example
  #### Using ``dirsearch``
  ```sh
  dirsearch -l examplesubdomains_alive.txt -i 200,204,403, -x 500,502,429 -R 5 --random-agent -t 100 -F -o directory.txt -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
  ```
  ### OR
  ```sh
  dirsearch -l examplesubdomains_alive.txt -i  -x 500,502,429,400,404 -R 5 --random-agent -t 100 -F -o directory.txt -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
  ```
  ### Getting old (`URLs`/ `artfacts`/ `parameter`) from WaybackMachine and other online archives
  #### Tools
  - `gau`
  ### Example
  #### Using `gau` to get old urls or `artfacts` or `parameter`
  ```sh
  cat examplesubdomains_alive.txt|gau > paras.txt
  ```
  - Now after get urls we want to filter urls with parameter.
  #### Tools
  - `uro`
  #### Example
    ```sh
    cat paras.txt| uro -o filterparam.txt
    ```
  - Filtering `.js` files
    
    ```sh
    cat filterparam.txt|grep ".js$" > jsfiles.txt
    ```
  then add some new lines
  #### Tools
  - `anew`

    ```sh
    cat jsfiles.txt | uro | anew jsfiles.txt
    ```
  now let's find hidden secrets within jsfiles
    #### Tools
  - `SecretFinder`

    ```sh
    cat jsfiles.txt| while read url; do python3 /home/arisemyafrica/Tools/secretfinder/SecretFinder.py -i $url -o cli >> secret.txt; done
    ```
  
  
