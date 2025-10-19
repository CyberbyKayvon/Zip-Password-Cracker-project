# Zip-Password-Cracking-project
This script performs a dictionary attack on password-protected `.zip` files using multithreading. It attempts to extract the archive using each password from a provided wordlist.

## Objective
- Demonstrate brute-force zip cracking using Python
- Learn how to use threading for faster execution
- Practice argument parsing and file handling

## Line-by-Line Breakdown

#!/usr/bin/python
# -*- coding: utf-8 -*-

from optparse import OptionParser
from threading import Thread
import zipfile
import sys

found = False  # Global flag to stop once password is found

def extractFile(zFile, password):
    global found
    try:
        zFile.extractall(pwd=password.encode('utf-8'))
        print("[+] Password Found:", password)
        found = True
        sys.exit(0)
    except:
        pass

def main():
    parser = OptionParser("usage: %prog -f <zipfile> -d <dictionary>")
    parser.add_option("-f", dest="zname", type="string", help="specify zip file")
    parser.add_option("-d", dest="dname", type="string", help="specify dictionary file")
    (options, args) = parser.parse_args()

    if (options.zname is None) or (options.dname is None):
        print(parser.usage)
        exit(0)
    else:
        zname = options.zname
        dname = options.dname

    try:
        zFile = zipfile.ZipFile(zname)
    except:
        print("[-] Failed to open zip file.")
        return

    try:
        passFile = open(dname, 'r', errors='ignore')
    except:
        print("[-] Failed to open dictionary file.")
        return

    for line in passFile.readlines():
        if found:
            break
        password = line.strip()
        t = Thread(target=extractFile, args=(zFile, password))
        t.start()

if __name__ == '__main__':
    main()
