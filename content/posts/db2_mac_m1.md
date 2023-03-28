---
title: Connecting to IBM DB2 on MacOS with Python (M1)
type: page
description: A guide on how to connect to Db2 using Apple Silicon
topic: Python
---


 {{< figure src="https://i.imgur.com/oS1owgB.png" title="" >}} 

Despite Apple launching their ARM-based processors in late 2020, IBM Db2 still does not support using the IBM ODBC driver on Apple Silicon (M1, M2, etc). Please support [this request for an Apple silicon version of the IBM Db2 driver.](https://ibm-data-and-ai.ideas.ibm.com/ideas/DB2CON-I-92). 


I've tried a [few](https://alexo.dev/blog/db2-on-macos-python-2/) [workarounds](https://github.com/ibmdb/python-ibmdb#pre-requisites) but so far only the methods below have been successful. This guide assumes you are using an IDE - I primarily use PyCharm but will update with Visual Studio Code.

### What you'll need: 

* Apple product with Apple Silicon (M1+)

* A Python [virtualenv](https://docs.python.org/3/library/venv.html)

* Packages: [ibm-db](https://pypi.org/project/ibm-db/), [ibm-db-sa](https://pypi.org/project/ibm-db-sa/), [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) (if using ODBC), [JayDeBeApi](https://pypi.org/project/JayDeBeApi/) (if using JDBC)
 
 * * *

### Connecting using ODBC (preferred)

1. Download [libstdc++.6.dylib](https://github.com/rahulkalluri/personal-site/blob/master/assets/libstdc%2B%2B.6.dylib). This file will make your ibm-db package compatible with your hardware.

2. Overwrite the existing file inside your virtual environment directory: 

```bash
~/example_project/venv/lib/python3.9/site-packages/clidriver/lib
```

*This directory will not exist if you haven't installed the prerequisite packages yet!*

3. Set an environment variable pointing to the location of the file. 
    
    * If using PyCharm: In the Run/Debug Configurations window, open the `Environment Variables` box. Here, add an line that contains the path to the file. Example:
    `DYLD_LIBRARY_PATH=~/Projects/example_project/venv/lib/python3.9/site-packages/clidriver/lib`

    {{< figure src="https://i.imgur.com/hB0uXRm.png" title="" >}}


    * If using Visual Studio Code: (Coming soon)


    * If using plain old Python: 
    
    ```python
    import os 
    os.environ['DYLD_LIBRARY_PATH'] = /project_folder/venv/lib/python3.9/site-packages/clidriver/lib
    ```

4. Run your code. **Don't skip this step.** macOS will throw a security error stating that it can't use the new file as it cannot verify the origin of the file.

5. To bypass this warning, go to `System Preferences > Security & Privacy`. Near the bottom, make sure you allow MacOS to use the new .lib file.

6. Run your code again. Now, macOS will provide the option for you to open the file. Select `Open`.

    {{< figure src="https://i.imgur.com/9fkuyQw.png#center" title="">}}

7. Profit!

Below is an example db2 connection function using an ODBC connection:

```python
def get_db2_connection():
    """
    Connects to a Db2 server then creates an engine and connection to that space
    :return: an sqlalchemy engine
    """
    # Assumes you have credentials stored in a dictionary called env_creds
    # Tweak this URL to mimic your actual JDBC URL. Not all Db2 connections require SSL
    sqla_url_f = f"db2+ibm_db://{env_creds['username']}:{env_creds['password']}@{env_creds['hostname']}:" \
                 f"{env_creds['port']}/{env_creds['database']};{env_creds['ssl']};" \
                 f"{env_creds['trustpass']};{env_creds['truststore']};"
    try:
        engine = create_engine(sqla_url_f, pool_size=10, max_overflow=20)
        conn = engine.connect()
        log.info('Successfully initiated Db2 connection')
        return engine

    except exc.SQLAlchemyError as se:
        log.error(f'Failed to initiate Db2 connection', exc_info=se)
        raise se

```

#### Using the terminal
If you'd like to use the terminal for the early steps, you can run the following commands (substitute with your own details). You will still need to follow steps 4-6 to ensure macOS recognizes the file.

```bash
# Download file to user's Downloads folder
wget -O ~/Downloads/libstdc++.6.dylib https://github.com/rahulkalluri/personal-site/blob/master/assets/libstdc%2B%2B.6.dylib

# Move file into project's virtual environment
mv -f ~/Downloads/libstdc++.6.dylib ~/example_project/venv/lib/python3.9/site-packages/clidriver/lib/

# Set the environment variable 
export DYLD_LIBRARY_PATH="~/example_project/venv/lib/python3.9/site-packages/clidriver/lib"
```

* * * 

### Connecting using JDBC

1. Install JDK 11 on your device. I recommend following my guide to installing Python using Homebrew. 

```bash
# Set your bash environment to 64-bit mode
arch -x86_64 zsh 

# Install JDK 11 using Homebrew
brew install java11
```

2. After installing java you'll need to create a hardlink for your machine to access it

```bash
sudo ln -sfn /usr/local/opt/openjdk@11/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-11.jdk
```

3. I recommend adding the JDK to your `.zshrc` file to make it accessible to other applications 

```bash
echo 'export PATH="/usr/local/opt/openjdk@11/bin:$PATH"' >> ~/.zshrc
export CPPFLAGS="-I/usr/local/opt/openjdk@11/include"
```

4. Download the latest [IBM Db2 drivers](https://www.ibm.com/support/pages/db2-jdbc-driver-versions-and-downloads). Copy `db2jcc.jar` and `db2jcc4.jar` to your project directory (`~/Projects/example-project/config/`)

5. Profit! 

Below is an example db2 connection function for JDBC connections:

```python
def get_db2_connection():
    """
    Connects to the Db2 server then creates an engine and connection to that server. Uses JDBC connection type.
    :return: aa sqlalchemy engine
    """

    # Assumes you have credentials stored in a dictionary called env_creds
    # Tweak this URL to mimic your actual JDBC URL. Not all Db2 connections require SSL
    try:
        conn = jaydebeapi.connect("com.ibm.db2.jcc.DB2Driver",
                                  `f"jdbc:db2://{env_creds['hostname']}:{env_creds['port']}/{env_creds['database']}"
                                  `f":sslConnection=True;", [f"{env_creds['username']}", f"{env_creds['password']}"],
                                  `jars=r'config/db2jcc.jar')
        log.info(f'{environment} Successfully initiated Db2 connection')
    except sqlalchemy.exc.SQLAlchemyError as se:`
        `log.error(f'{environment} Failed to initiate Db2 connection', exc_info=se)
    engine = conn.cursor()
    return engine

```