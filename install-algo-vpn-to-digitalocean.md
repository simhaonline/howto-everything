# Install Algo VPN to Digital Ocean (from MacOS)

[Algo](https://github.com/trailofbits/algo) is an easy way to set up a personal VPN in the cloud. 


## Install server

In case something is missing here, refer to their [DO install guide](https://github.com/trailofbits/algo/blob/88eaf30e65df5ac2128f035954b275608f380fc1/docs/cloud-do.md) or [Docker client guide](https://github.com/trailofbits/algo/blob/61729ac9b56de477efe87f7d2a4fa5dd7a7af2ae/docs/deploy-from-docker.md).


Generate an API token in Digital Ocean, then run:

```
export DO_TOKEN=<your-token-herer>

git clone git@github.com:trailofbits/algo.git
cd algo

ALGO_ARGS="provider=digitalocean
  server_name=algo
  ondemand_cellular=true
  ondemand_wifi=true
  dns_adblocking=false
  ssh_tunneling=false
  store_pki=true
  region=fra1
  do_token=$DO_TOKEN"
                                
docker run --cap-drop=all -it \
  -e "ALGO_ARGS=$ALGO_ARGS" \
  -v $(pwd):/data \
  trailofbits/algo:latest
```

The installation takes quite a while. After it's finished, you should see the following banner. 

```
ok: [x.x.x.x] => {
    "msg": [
        [
            "\"#                          Congratulations!                            #\"",
            "\"#                     Your Algo server is running.                     #\"",
            "\"#    Config files and certificates are in the ./configs/ directory.    #\"",
            "\"#              Go to https://whoer.net/ after connecting               #\"",
            "\"#        and ensure that all your traffic passes through the VPN.      #\"",
            "\"#                     Local DNS resolver X.X.X.X, fdxx::x:xxxx                   #\"",
            ""
        ],
        "    \"#        The p12 and SSH keys password for new users is xxxxxxxx       #\"\n",
        "    \"#        The CA key password is xxxxxxxxxxx       #\"\n",
        "    \"#      Shell access: ssh -i configs/algo.pem root@x.x.x.x        #\"\n"
    ]
}
```

The default configuration is taken from `config.cfg` file, and contains three users: 

* desktop
* laptop 
* phone


### Save the secrets

1. Save the output banner which contains the passwords etc to 1password 

    This is needed for possibly updating users later. **Note:** updating users will create new p12 and SSH key passwords for new users. 
    
2. You can optionally save the configs to e.g. 1password. `zip -r algo-configs.zip configs` and save the zip file to 1password

    Needed for easy reconfiguration of a device. Optional, because if you lose the keys, you can always reinstall the server.


## Install clients

*Refer to https://github.com/trailofbits/algo#configure-the-vpn-clients for full client guides.*



### MacOS

*WireGuard is an open source VPN client, which makes installing the client VPN configs easier. See https://www.wireguard.com/.*   

Install WireGuard at https://apps.apple.com/us/app/wireguard/id1451685025?mt=12 and open the app. Click "install via configuration file" or similar and browse to `configs/x.x.x.x/wireguard/laptop.conf`.
    
### iOS

Install WireGuard at https://itunes.apple.com/us/app/wireguard/id1441195209?mt=8, and scan the QR code from your computer. 

To show the QR code, run *(where x.x.x.x is the IP address of the created Droplet)*:

```
open configs/x.x.x.x/wireguard/phone.png
```    


## Cleaning up secrets after installation

Run `rm -rf configs` in the algo repository directory after you have installed the VPN clients. WireGuard copies the secrets to its own storage so removing the configs folder is fine.

If you stored the configs as a zip in 1password, it can be always unzipped to the same place to e.g. update users.


## Update users

* Go to algo project dir
* Update config.cfg to contain wanted users
* Run update users

    ```
    docker run --cap-drop=all -it \
      -e "ALGO_ARGS=update-users" \
      -v $(pwd):/data \
      trailofbits/algo:latest
    ```

## Glossary

* **On demand** = automatic VPN connection
