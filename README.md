# dotnet-devcert-linux
aspnet core development over ssl using self-signed localhost certificates on linux. This example was developed using Arch Linux

## Dev Cert issue on Linux
dotnet core ships with a utiltiy to create and trust certificates to enable ssl for development. 

`dotnet dev-certs https --trust`

Unfortunately there is no standard certificate store across the linux distributions, so the trust option is not avalible on linux.

## Solution
The easiest option that I found was to ignore the `dev-certs` tool and just manually create and trust a localhost certificate. 

This project includes [dev-cert.sh](dev-cert.sh) bash script which creates a CA certificate, localhost certificate, as well as a pfx file for asp.net.
This will place the files in the dotnet default directory for certificates `$HOME/.aspnet/https` . To install run the following.

1. `chmod +x dev-cert.sh`
2. `./dev-cert.sh`
3. Provide password for the certificate when prompted
4. `sudo trust anchor $HOME/.aspnet/https/ca.crt` or any directory you can put. edit the `.sh` file for proper path mapping. e.g. `home/user/https/`
5. Update the app launch.json with the certificate's path & password
    `"env": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_Kestrel__Certificates__Default__Password": "password",
        "ASPNETCORE_Kestrel__Certificates__Default__Path": "${env:HOME}/.aspnet/https/localhost.pfx"
      }`
6. The browser will need to be restarted. Be sure the stop chrome background tasks as well if using chrome.

Step 4 will vary on your linux distibution. This works on Arch Linux.

This should only need to be setup once per development machine. Going forward, you will only need to perform step 5 to copy the certificate environment variables into the new project's launch.json.


