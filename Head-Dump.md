# Heap Dump Challenge Writeup - PicoCTF 2025

## Challenge Overview
We are presented with a news site that contains an API documentation link. Our goal is to explore the API, extract useful information, and retrieve the flag.

## Step 1: Discovering the API Documentation
Upon visiting the site, we find a tag pointing to the Swagger documentation of the site’s API. We navigate to `/api-docs/` to explore the available endpoints.

![Site Screenshot](https://github.com/v0idsec/PicoCTF-2025/blob/main/images/Head-Dump/Site.png)

## Step 2: Identifying the Vulnerable Endpoint
From the API documentation, we find an interesting endpoint called `heapdump`. This endpoint provides a snapshot of the server’s memory, which may contain sensitive information.

![API Documentation](https://github.com/v0idsec/PicoCTF-2025/blob/main/images/Head-Dump/APIinfo.png)

## Step 3: Extracting the Heap Dump
To retrieve the heap dump file, we use the following `curl` command:

```sh
curl http://site-addr/heapdump -o heapdump
```

This downloads the heap dump file to our local system.

![Heap Dump Retrieved](https://github.com/v0idsec/PicoCTF-2025/blob/main/images/Head-Dump/Curl%20Result.png)

## Step 4: Searching for the Flag
A heap dump may contain various memory artifacts, including sensitive data. We use the `strings` command to filter out readable text and search for the flag:

```sh
strings heapdump | grep pico
```

This reveals the flag embedded in the memory dump:

```
picoCTF{Pat!3nt_15_Th3_K3y_439bb394}
```

## Flag
**picoCTF{Pat!3nt_15_Th3_K3y_439bb394}**

## Conclusion
By identifying an exposed heap dump endpoint, we were able to extract a memory snapshot and search for the flag using basic Linux commands. This challenge demonstrates the risks of exposing sensitive debugging endpoints in production environments.

