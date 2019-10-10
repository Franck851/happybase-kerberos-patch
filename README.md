# happybase-kerberos-patch
[![Build Status](https://travis-ci.org/normal-cock/happybase-kerberos-patch.svg?branch=develop)](https://travis-ci.org/normal-cock/happybase-kerberos-patch)
[![Coverage Status](https://coveralls.io/repos/github/normal-cock/happybase-kerberos-patch/badge.svg?branch=master)](https://coveralls.io/github/normal-cock/happybase-kerberos-patch?branch=master)
<!-- TOC -->

- [happybase-kerberos-patch](#happybase-kerberos-patch)
    - [Introduction](#introduction)
    - [requirements](#requirements)
    - [Installment](#installment)
    - [Usage:](#usage)
    - [Important points to remember](#important-points-to-remember)

<!-- /TOC -->

## Introduction

This is a patch for happybase to support kerberos when connect to hbase thrift server with the following extra features:

1. `KerberosConnectionPool` support multiple specify multiple hosts as destination to connection as a support to high avaliable
2. `KerberosConnectionPool` will auto connect to the next host if current is unavailable even in the outermost with statement
3. Using `pykerberos` instead of `kerberos` which has memory leaking issue

The patch now support `python 2.7`, `python 3.5`, `python 3.6`, `python 3.7`.

## requirements

In order to use kerberos, the following packages is required:

1. python3-dev
2. libkrb5-dev

For example, in ubuntu install by the follow commands:

```shell
sudo apt-get install python-dev
sudo apt-get install python3-dev
sudo apt-get install libkrb5-dev
```

## Installment

`pip install -U git+https://github.com/normal-cock/happybase-kerberos-patch.git`

## Usage:

```python
from happybase_kerberos_patch import KerberosConnection, KerberosConnectionPool
connection = KerberosConnection('HOST_TO_THRIFT_SERVER', protocol='compact', use_kerberos=True)
test_table = connection.table('test')
# insert
test_table.put('row_key_1', {'f1:q1':'v1'})
# get data
print test_table.row('row_key_1')

pool = KerberosConnectionPool(size=3, host='...', protocol='compact', use_kerberos=True)
with pool.connection() as connection:
    test = connection.table('test')
    print test_table.row('row_key_1')

# multiple thrift servers high avaliable
pool = KerberosConnectionPool(size=3, hosts=['thrift1', 'thrift2'], protocol='compact', use_kerberos=True)
with pool.connection() as connection:
    test = connection.table('test')
    print test_table.row('row_key_1')
```

## Important points to remember

* Only support "compact" protocol and "buffered" transport
* Only support happybase>=1.2.0
