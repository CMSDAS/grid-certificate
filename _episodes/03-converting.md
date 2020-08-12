---
title: "Converting the .p12 certificate for use with voms-proxy-init"
teaching: 5
exercises: 0
questions:
- "How can I convert the certificate I just downloaded to get a grid proxy?"
objectives:
- "Know the `openssl` commands to convert from `p12` to `pem`"
- "Be aware of where the `pem` files need to be located"
keypoints:
- "From the downloaded certificate two separate files need to be extracted."
- "The extracted user key and certificate files need to be in your `~/.globus` directory."
- "These files should only be readable for you/"
---
The grid certificate needs to be converted from the file format (`*.p12`) you downloaded from the CA website (see also [these instructions][CA_VOMS]). These converted files need to be put into a directory within your home directory to be discovered automatically. Let's create this directory if it doesn't exist yet, and delete possibly old files therein:

~~~
mkdir -p ~/.globus
rm -f ~/.globus/usercert.pem
rm -f /~/.globus/userkey.pem
~~~
{: .language-bash}

From the `*.p12` file we will need to extract a user certificate and a user key. The file you downloaded will probably be called `myCertificate.p12`. In the terminal, change to the directory to which you downloaded this file (e.g. `~/Downloads`) or move the file to your current working directory. Let's start extracting the user certificate:

~~~
openssl pkcs12 -in myCertificate.p12 -clcerts -nokeys -out $HOME/.globus/usercert.pem
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
openssl pkcs12 -in myCertificate.p12 -nocerts -out $HOME/.globus/userkey.pem
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
chmod 600 userkey.pem
chmod 600 usercert.pem
~~~
{: .language-bash}

Now you need to move the files to the correct location to be discovered automatically, which is in your home directory:

~~~
mkdir -p ~/.globus
mv userkey.pem ~/.globus
mv usercert.pem ~/.globus
~~~
{: .language-bash}

Try if this works.

{% include links.md %}

[CA_VOMS]: https://ca.cern.ch/ca/Help/?kbid=024010