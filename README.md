# DOC  
Documentation, Of Course!  
  
A tool for automatically organizing and searching for host info when doing pentests or CTFs.  
With doc, you'll always know what's up!  
  
<b>Highlights:</b>  
• Search by hostname/alias, IP, or port.  
• Automatically formats host info into neat little cards. Pretty!  
• Useful for automation:  
  • Why remember and type a whole address when you can just do `$(doc [name] ip)` or `$(doc [name] url)`?  
  • Scan all hosts with a certain port like so: `for x in $(doc list -p [port number]); do [command] "$x"; done`  
• Ingests nmap and nxc output.  
• Extensible - Just add JSON!  
• Easily editable per-host to-do lists with built-in color coding.  
• Optional GPG integration.  
  
<b>Installation:</b>  
Run `sudo apt-get install -y ripgrep sd jq`  
That should cover most functionality.  
  
## Usage  
<b>Actions</b>  
`doc -c [name]`               -  Create new host info card.
`doc -u [name] [field] [x]`   -  Update host info.
`doc -u [name] auto`          -  Attempt to find hostname and OS automatically.
`doc [name] [property]`       -  Print property for a particular host.  
`doc [name] [user|pass]`      -  Prints most recently added username or password.  
`doc [name] [user|pass] [n]`  -  Prints nth username or password.  
`doc [name(s)]`               -  Print info cards for the provided hosts.  
`doc all`                     -  Print all host cards at once!  
`doc all [text]`              -  Print host cards whose names or IPs include the provided text.  
`doc list`                    -  List the names and IPs of all hosts.  
`doc list names`              -  List the names of all hosts.  
`doc list ips`                -  List the IPs of all hosts.  
`doc list -p [port]`          -  List the IPs of all hosts with a certain port.  
`doc clear`                   -  Clear the card cache. Useful if editing host JSON directly.  
`doc target [name]`           -  Define a particular host as the current target for `doc x`.  
`doc x [...]`                 -  Useful for scripting. If another script uses `doc x`, you can set targets for it dynamically.  

  
<b>Updating Host Info</b>  
New host cards are created using `doc -c [name]`.  
In general, hosts are updated using the syntax `doc -u [name] [field] [value]`.  
If a target exposes SMB or MSSQL, it may be possible to retrieve the host name and OS version with `doc -u [name] auto`.  

Update ports:
`doc -u [name] ports [number]`  
If possible, the most likely service for that port will be added automatically.  
The "os", "ip", and "url" fields can be updated similarly.  
Running `doc -u [name] ports [file]` on an nmap scan log will add all found ports.
  
Update credentials:   
`doc -u [name] users [username] (password)`  
For production credentials, please consider setting `use_gpg=1` in the settings at the top of the script.  
This will ensure that passwords are stored GPG-encrypted using `pass`.  
  
Update to-do list:  
`doc -u [name] todo`  
This will open the text editor defined by the `$EDITOR` environment variable.  
After that, you can add a list like the one shown below.  
```
[ ] Incomplete
[X] Completed
[?] Unknown/possible
[!] Urgent/warning
```
  
## Examples  
The following examples will use a HackTheBox lab environment with user and flag data replaced by sample text.  
  
The "extract" script provides examples of automatic data extraction/ingestion using Nmap and NetExec.  
Some of this functionality is now provided by `doc -u [name] ports [file]` and `doc -u [name] auto`.  
`extract -n [IP] [name]` will create a basic info card using results from nmap.  
![doc](examples/doc.png)
  
Observe how `$(doc [name] ip)` can be used in place of addresses.  
If you have access to a bastion host like this one, you can use `extract -s -p [network address]` to scan for Windows hosts on the internal network via SMB.  
The `-p` flag uses proxychains to allow NetExec to reach the LAN. This will create host cards that includes the IP, OS version, and hostname of machines on the specified network.  
![doc2](examples/doc2.png)
  
The cards can now be searched by name and IP.  
`doc list -p 445` will return all SMB-capable IPs for use in further scans.  
![doc3](examples/doc3.png)
  
Examples of host cards including various fields.  
Note the automatic color-coding of the to-do list based on the presence of "?" or "!" in the checkbox.  
![doc4](examples/doc4.png)
