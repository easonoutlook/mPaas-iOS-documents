@cnName 1 Hotpatch
@priority 1

# 1 Hotpatch

[TOC]

## 1.1 Introduction

### 1.1.1 Components

App Store has a review process for distributing applications, which is not as flexible as it is on the Android platform.  Hotpatch offers a quick way to fix severe bugs of a released application without the need to release a new version. 

Hotpatch framework is located in the MPCommandCenter in mPaaS, including two modules, namely Command Push and Dynamic Patching. 

'Command Push': This module is in charge of synchronizing scripts with the server, and controlling the running version and timing of scripts. 

'Dynamic Patching': This module executes the Lua scripts and completes the hotpatch by replacing the Objective-C method. 

### 1.1.2 Working procedures

* The application is started, and Hotpatch executes scripts downloaded and stored to a local place. 

* The application is started, and Hotpatch communicates with the server and downloads scripts. Once the download is complete, it executes the new scripts immediately. 

* The application switches from the background to the foreground, and Hotpatch communicates with the server and synchronizes the scripts. Once the download is complete, it executes the new scripts at next application startup. 

## 1.2 Publishing scripts

After the local verification is successful, the developer can deploy the scripts to be published on the server.  The percentage and user ID for the push can be configured.  (This feature is under development.)

### 1.2.1 Signature verification of scripts

Hotpatch has a self-defined signature verification procedure to ensure security and correct script sources.   The signature verification procedure is as follows: 

* Read the Lua script content, and encrypt the content character strings using MD5. 
* Read the Lua script content in the binary format, and encrypt the binary contents using AES128 encryption. 
* Create a zip file and include the above two content items into the zip file. 
* Read the zip file above in the binary format, and encrypt the zip file again using AES128 encryption. The further encrypted data is saved as a zip file. 
* Read the zip file above in the binary format, and sign the zip file using the SHA1 algorithm. 
* Read the zip file above in the binary format, add the data delimiter, signature data and form the .js scripts. 

The client requests the .js scripts, and gets the real scripts for execution by following the reversed order of the steps above. 

### 1.2.2 How to use Lua2Js tool

I. Generate your own Lua2Js tool



II. How to use

* Click the "Select Lua Scripts" button. 
![ca33c9ec32722af65948a0584d52ec7a](https://os.alipayobjects.com/rmsportal/sNQPUqUdCqiMCUw.png)

* Select the desired .lua scripts for conversion, and click the "Open" button. 
![87e5307d18a4d50a177ce7df0447d77e](https://t.alipayobjects.com/images/rmsweb/T1cUxfXcJfXXXXXXXX.png)

* The .lua scripts are converted to .js scripts successfully. 
![85960aa78c570154e4add0e9fb61d459](https://t.alipayobjects.com/images/rmsweb/T1R_RfXlVqXXXXXXXX.png)

## 1.3 Accessing Hotpatch on client

### 1.3.1 Managing key using symmetric encryption

Implement the interface under mPaasAppInterface to enable the Hotpatch module to call back the accessed application in order to acquire the key for the encryption in the Security Guard.  (The application needs to configure the key and secret to the Security Guard for management.)

```
#pragma mark Hotpatch

/**
 *  The key name configured in Security Guard for encrypting the Hotpatch scripts. After Hotpatch module gets this key, it will call the Security Guard interface for encryption.
 *
 *  @return The key name configured in Security Guard
 */
- (NSString*)appHotpatchScriptEncryptionKey;
```

### 1.3.2 Managing public key File using asymmetric encryption

Initialize your public key file by calling the method in APMobileSecurity library in the main function. 

```
APSecBufferRef APSecGetPKS()
{
    char sig[] = {};
    
    size_t len = sizeof(sig);
    APSecBufferRef buf = (APSecBufferRef)malloc(sizeof(APSecBuffer) + len);
    buf->length = len;
    memcpy(buf->data, sig, len);
    return buf;
}

int main(int argc, char *argv[])
{
    NSString *path;
    APSecBufferRef buf;
    int ret;
    
    path = [[NSBundle mainBundle] pathForResource:@"pubkey" ofType:@"pem"];
    APSecInitPublicKey([path UTF8String]);
    
    buf = APSecGetPKS();
    ret = APSecVerifyFile([path UTF8String], buf->data, buf->length);
    free(buf);
    if (ret != 0) {
        NSLog(@"The public key is modified.");
    }
    
	@autoreleasepool {
    	mPaasInit(@"12345", [mPaasAppInterfaceImp class]);
	    return UIApplicationMain(argc, argv, @"DFApplication", @"DFClientDelegate");
	}
}
```
