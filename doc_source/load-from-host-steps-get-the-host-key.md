# Step 4: Get the public key for the host<a name="load-from-host-steps-get-the-host-key"></a>

You can optionally provide the host's public key in the manifest file so that Amazon Redshift can identify the host\. The COPY command does not require the host public key but, for security reasons, we strongly recommend using a public key to help prevent 'man\-in\-the\-middle' attacks\. 

You can find the host's public key in the following location, where `<ssh_host_rsa_key_name>` is the unique name for the host's public key: 

```
:  /etc/ssh/<ssh_host_rsa_key_name>.pub
```

**Note**  
Amazon Redshift only supports RSA keys\. We do not support DSA keys\.

When you create your manifest file in Step 5, you will paste the text of the public key into the "Public Key" field in the manifest file entry\.