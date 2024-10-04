# Blackarch-linux-conflict-fixes

## Scripts and fixes for resolving common  package conflicts in BlackArch Linux after installation:
## 1- conflict:
-   `jre-openjdk`
-   `jre-openjdk-headless`
-   `jdk-openjdk`
 
 ****1-** Run the following command to create a list of the installed packages that depend on the       conflicting Java packages. This will help you reinstall them after resolving the conflicts:**
 
     pacman -Qi jre11-openjdk jre17-openjdk jdk11-openjdk jdk17-openjdk
     jre11-openjdk-headless jre17-openjdk-headless > ~/java-dependents.txt

**2- You can now remove the conflicting packages forcefully using the `-Rdd` option to bypass dependency checks. This may cause dependent packages to stop working temporarily, but we'll reinstall them after resolving the conflicts.**

     sudo pacman -Rdd jre11-openjdk jre17-openjdk jdk11-openjdk jdk17-openjdk jre11-openjdk-headless jre17-openjdk-headless
**3- Install the latest `jdk-openjdk` package to resolve the conflict:**

    sudo pacman -S jdk-openjdk
    
   

**4- Reinstall the packages that were relying on the removed Java runtime versions. You can do this by checking the `java-dependents.txt**

    sudo pacman -S $(grep "Required By" ~/java-dependents.txt | awk '{print $3}')
      
**This process should remove the conflicts while allowing you to restore the Java-dependent packages afterward.**

## 2- conflict:

    

> **> error: failed to commit transaction (conflicting files) uvicorn: > /usr/bin/uvicorn exists in filesystem (owned by python-uvicorn)**



**1- If you can afford to remove `python-uvicorn` temporarily, you can uninstall it, upgrade your system, and then reinstall `python-uvicorn` afterward**

    sudo pacman -Rns python-uvicorn sudo pacman -Syu sudo pacman -S python-uvicorn
**This method clears the conflict by removing `python-uvicorn`, upgrading the rest of the system, and then reinstalling the required packages.**

> error: failed to prepare transaction (could not satisfy dependencies)
> :: removing python-uvicorn breaks dependency 'python-uvicorn'


**2- This will create a list of all installed packages related to `python-uvicorn`, allowing you to reinstall them later.**

    pacman -Qi python-uvicorn | grep "Required By" | sed 's/Required By : //g' | tr ' ' '\n' > uvicorn-dependent-packages.txt

**3- This command will forcefully remove all packages listed in the file without considering dependencies (`-Rdd`).**

    sudo pacman -Rdd $(cat uvicorn-dependent-packages.txt) python-uvicorn

**This approach removes all packages relying on `theHarvester` and `python-uvicorn`, ensuring that any conflicting dependencies are also removed before reinstalling everything from scratch**
**4- Reinstall the packages that were removed by reading from the backup list:**

    sudo pacman -S $(cat uvicorn-dependent-packages.txt) python-uvicorn
    
**With this approach, you'll first back up the list of dependencies, forcefully remove the packages that depend on `python-uvicorn`, and finally reinstall everything smoothly.**
