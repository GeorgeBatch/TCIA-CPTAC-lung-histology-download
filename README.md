
# TCIA-CPTAC Lung: LUAD (adenocarcinoma) and LSCC (squamous cell carcinoma)

**Cohort selection portal:** https://www.cancerimagingarchive.net/histopathology-imaging-on-tcia/
* Cancer location: Lung

Note, for some reason, Adenocarcinoma and Squamous Cell Carcinoma do not show up if you choose "Cancer Type" to be "Lung Cancer"


**Data selection portal:** https://cancerimagingarchive.net/datascope/cptac/home/
* Topographic_Site: Lung


## Information from the web page

**Lung Cohorts**
* LUAD (Lung Adenocarcinoma)
	* 244 subjects
	* https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=33948253
* LSCC (Lung Squamous Cell Carcinoma)
	* 212 subjects
	* https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=33948248

Clicking "download cohort" downloads: [./tcia-luad-lusc-cohort.csv](./tcia-luad-lusc-cohort.csv)

## Guide for downloading with command line

The guide was sent by the TCIA Portal support team
[https://github.com/kirbyju/TCIA_Notebooks/blob/main/TCIA_Aspera_CLI_Downloads.ipynb](https://github.com/kirbyju/TCIA_Notebooks/blob/main/TCIA_Aspera_CLI_Downloads.ipynb)

## Getting the data onto a remote server if you do not have the ability to download to the server directly

1. If you do not have 450GB free on your computer (for downloading and uploading one package at a time), get an external hard drive and make it writable
2. Download LUAD and LSCC with Aspera Connect onto the external drive (one by one if needed) - location of download can be changed in Aspera Connect App's settings. Aspera Connect ensures the integrity of the files so at this point the files should be intact.
3. Compute the `md5sum` codes for the locally downloaded files. We will compare them with the md5sum codes after uploading data onto the remote server.
    * Linux with `md5sum`: 
        ```shell
        # sorting makes it easier to compare to the remote server version
        find . -type f -name "*.svs" | sort | xargs md5sum > local_hashes.txt
        ```
    * Mac with `md5`
        ```shell
        # adding -r produces linux-like format
        # sorting makes it easier to compare to the remote server version
        find . -type f -name "*.svs" | sort | xargs md5 -r > local_hashes.txt
        ```
    * Mac with `md5` and `parallel` to make it faster
        ```shell
        brew install parallel

        # adding -r produces linux-like format
        # sorting makes it easier to compare to the remote server version
        # parallel -k results in the same order, while omitting -k might not preserve the order
        find . -type f -name "*.svs" | sort | parallel -k md5 -r > local_hashes.txt
        ```

3. Upload from the external drive onto the cluster using `rsync` with flag `-aP`. See [rsync tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories).

4. Compute the remote server hashes
```shell
# sorting makes it easier to compare to the remote server
find . -type f -name "*.svs" | sort | xargs md5sum > server_hashes.txt
```

5. Copy `server_hashes.txt` to local machine or `local_hashes.txt` to the server using `scp` or `rsync`

4. Check the md5sum codes (local download vs server codes)
```
# format might still be different (2 spaces vs 1 space between the md5sum code and the path to the file)
# use something like that to replace 2 spaces with 1 space
# sed 's/  / /' server_hashes.txt > formatted_server_hashes.txt

# check the difference, if
# 1. the upload went without errors
# 2. the contents of the files are sorted 
# 3. the contents of the files have the same format, 
# then the command should produce an empty output and you can be reasonably sure you succeded
diff local_hashes.txt formatted_server_hashes.txt
```

My md5sum_hashes are in [md5sum_hashes.txt](./md5sum_hashes.txt), but they are not the official ones. I was not able to found any md5sum hashes shared by the TCIA team.