# Admin User Guide

Copyright © 2022 by Symetry, Inc. 14 Pine Street, Ste 6 Morristown, NJ 07960 All Rights Reserved November 8th, 2022

## Introduction

The SymetryML Admin CLI is a command-line application that allows administrators to manage service updates and user credentials. This user guide describes the common usage patterns for the SymetryML Admin CLI.

## Display Available Commands

This command displays all the available commands in the SymetryML CLI,

```
symetry.sh help
```

## Software Version

This command performs version checks within SymetryML. Version checks also are performed automatically when a user logs into the SymetryML Web application.

```
symetry.sh version
```

For request regarding updating your SymetryML software please contact [support@symetryml.net](mailto:support@symetryml.net)

## Manage User Credentials

### Show User Credential Types

Display the current user credentials.

```
symetry.sh showCredentials
```

### Change User Credentials

This command changes user credentials.

```
symetry.sh newkey <USER> <EXPIRY>
```

where `USER` is the name of the user whose credentials you want to change. `EXPIRY` is seconds until expiry.

### New User

Create a new user and display the corresponding credentials.

```
symetry.sh addUser <USER> <EXPIRY>
```

where `USER` is the name of the new user. `EXPIRY` is seconds until expiry.
