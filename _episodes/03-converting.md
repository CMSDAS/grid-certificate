---
title: "Converting the .p12 certificate for use with voms-proxy-init"
teaching: 5
exercises: 0
questions:
- "How can I convert the certificate I just downloaded to get a grid proxy?"
objectives:
- "Know the `openssl` commands to convert from `p12` to `pem`."
- "Be aware of where the `pem` files need to be located."
keypoints:
- "From the downloaded certificate two separate files need to be extracted."
- "The extracted user key and certificate files need to be in your `~/.globus` directory on the machine on which you would like to obtain a grid proxy."
- "These files should only be readable for you."
---
The grid certificate needs to be converted from the file format (`*.p12`, which mean `pkcs12` format) you downloaded from the CA website (see also
[these instructions][CA_VOMS]). You will probably want to obtain the grid proxy on a
remote machine, e.g. your institute's local cluster or on LXPLUS. You therefore need
to first copy the downloaded `*.p12` to the remote machine.
The file you downloaded will probably be called `myCertificate.p12`. In the terminal,
change to the directory to which you downloaded this file (e.g. `~/Downloads`) or move
the file to your current working directory for the following commands to work without
having to adjust them.

To copy the file `*.p12` For example, when using LXPLUS, do the following:

~~~
scp myCertificate.p12 lxplus.cern.ch:~/
~~~
{: .language-bash}

Then log on to an LXPLUS machine:

~~~
ssh lxplus.cern.ch
~~~
{: .language-bash}

You should now see the command prompt on the LXPLUS machine.

> ## Working remotely
>
> All following commands will be executed on LXPLUS (or the remote machine of your choice).
> However, they could also work on your local computer in case you would like to obtain a
> GRID proxy there, but this cannot be guaranteed.
{: .callout}

The files that we will generate from the `*.p12` file need to be put into a directory
within your home directory to be discovered automatically. Let's create this directory
if it doesn't exist yet, and delete possibly old files therein:

~~~
mkdir -p ~/.globus
rm -f ~/.globus/usercert.pem
rm -f ~/.globus/userkey.pem
~~~
{: .language-bash}

From the `*.p12` file we will need to extract a user certificate and a user key. Let's start extracting the user certificate:

~~~
openssl pkcs12 -in myCertificate.p12 -clcerts -nokeys -out ~/.globus/usercert.pem
~~~
{: .language-bash}

This will ask for the import password that you set before downloading the file from the CA:

~~~
Enter Import Password:
MAC verified OK
~~~
{: .output}

Now on to obtaining the `userkey.pem`:

~~~
openssl pkcs12 -in myCertificate.p12 -nocerts -out ~/.globus/userkey.pem
~~~
{: .language-bash}

This will again ask for the import password as above, and in addition will ask you to enter a `PEM pass phrase`. This is the password that you will have to enter each time you would like to obtain a new VOMS proxy, remember it well.

~~~
Enter Import Password:
MAC verified OK
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
~~~
{: .output}

One thing that is important are correct access rights (or file modes). Only you should be able to read the `userkey.pem`. Execute the following commands for this purpose:

~~~
chmod 600 ~/.globus/userkey.pem
chmod 600 ~/.globus/usercert.pem
~~~
{: .language-bash}

Now you need to move the files to the correct location to be discovered automatically, which is in your home directory:

Try if this works:

~~~
voms-proxy-init --voms cms
~~~
{: .language-bash}

This will ask for you `GRID pass phrase`, which is the `PEM pass phrase` you set above. The output should then be similar to what is shown below:

~~~
Enter GRID pass phrase for this identity:
Contacting voms2.cern.ch:15002 [/DC=ch/DC=cern/OU=computers/CN=voms2.cern.ch] "cms"...
Remote VOMS server contacted succesfully.


Created proxy in /tmp/x509up_u501.

Your proxy is valid until Wed Aug 12 22:15:57 CEST 2020
~~~
{: .output}

You can now delete the `myCertificate.p12` file to avoid security issues.

{% include links.md %}

[CA_VOMS]: https://ca.cern.ch/ca/Help/?kbid=024010