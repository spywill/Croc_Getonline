# Croc_Getonline

## INTRODUCTION :
  - This project is developed for the HAK5 KeyCroc
  - Attempt to connect Keycroc automatically to target wifi access point.

* **TESTED ON**
  - Windows 10
  - Raspberry pi 4 (bullseye image)
  - linux (parrot os)

## INSTALLATION :

  - Enter arming mode on your keycroc to install file.
  - Download the Croc_getonline.txt payload and Place this in the KeyCroc **payload folder**

## STARTING GETONLINE :

   - After install plug into target and type in anywhere
   - **getonline_W** <-- MATCH word for windows
   - **getonline_L** <-- MATCH word for Linux
   - **getonline_R** <-- MATCH word for Raspberry pi
   - When the payload is done running the LED will light up green
   - Keycroc should now be connected to target wifi access point
   - NOTE: for linux edit payload for passwd needed for sudo permission

## PAYLOAD INFO :

**(netsh wlan show networks) | Select-String "\:(.+)$" | % {\$name=$_.Matches.Groups[1].Value.Trim(); $_} | %{(netsh wlan show profile name="$name" key=clear)} | Select-String "Key Content\W+\:(.+)$" | % {$pass=$_.Matches.Groups[1].Value.Trim(); $_} | %{[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }} | Out-File -Encoding UTF8**

netsh wlan show networks: This command displays a list of all available wireless networks and their corresponding information, including their SSID and signal strength.

| Select-String "\:(.+)$": This command selects only the lines that contain the network names by using regular expressions. Specifically, it searches for lines that end with a colon followed by one or more characters, and selects only those lines.

| % {\$name=$_.Matches.Groups[1].Value.Trim(); $_}: This command assigns the network name to the variable $name, which is extracted from the previously selected lines. It also trims any whitespace from the name.

| %{(netsh wlan show profile name="$name" key=clear)}: This command uses the network name to retrieve the security information for each network. Specifically, it displays the profile information for the specified network, including the security key (if available).

| Select-String "Key Content\W+\:(.+)$": This command selects only the lines that contain the security key information. Specifically, it searches for lines that contain the text "Key Content" followed by a colon and one or more characters, and selects only those lines.

| % {$pass=$_.Matches.Groups[1].Value.Trim(); $_}: This command assigns the security key to the variable $pass, which is extracted from the previously selected lines. It also trims any whitespace from the key.

| %{[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }}: This command creates a custom object that contains the network name and security key for each network. Specifically, it creates an object with two properties: PROFILE_NAME (which contains the network name) and PASSWORD (which contains the security key).

| Out-File -Encoding UTF8: This command saves the custom object to a text file, using UTF-8 encoding. The file will contain a list of all available wireless networks and their corresponding security keys.


**t_pw=$(sudo sed -e '/ssid\ psk/,+1p' -ne ":a;$t_ssid/{n;h;p;x;ba}" /etc/wpa_supplicant/wpa_supplicant.conf | sed 's/[[:space:]]//g' | sed 's/psk="\(.*\)"/\1/')**

sudo sed -e '/ssid\ psk/,+1p' -ne ":a;$t_ssid/{n;h;p;x;ba}" /etc/wpa_supplicant/wpa_supplicant.conf: This command extracts the lines of the configuration file located at /etc/wpa_supplicant/wpa_supplicant.conf that contain the SSID and the corresponding PSK (pre-shared key) of the wireless network that the device is currently connected to. This is done by searching for the line that contains the SSID of the current network, and then extracting the line that follows it, which contains the PSK.

sed 's/[[:space:]]//g': This command removes all whitespace characters from the output of the previous command. This is done to ensure that the PSK is in the correct format for later use.

sed 's/psk="\(.*\)"/\1/': This command extracts the PSK from the output of the previous command. It does this by searching for the string "psk=" followed by a double-quoted string, and then extracting the contents of the double-quoted string. The result is assigned to the variable $t_pw.

In summary, this code extracts the PSK of the wireless network that the device is currently connected to and assigns it to the variable $t_pw.



**sed -E -i '1{x;s#^#sed -n 1p wifipass.txt#e;x};10{G;s/\n(\S+).*/ \1/};11{G;s/\n\S+//}' config.txt** 

By default, sed reads each line of a file. For each cycle, it removes the newline, places the result in the pattern space, goes through a sequence of commands, re-appends the newline and prints the result e.g. sed '' file replicates the cat command. The sed commands are usually placed between '...' and represent a cycle, thus:

1{x;s#^#sed -n 1p wifipass.txt#e;x}

1{..} executes the commands between the ellipses on the first line of config.txt. Commands are separated by ;'s
x sed provides two buffers. After removing the newline that delimits each line of a file, the result is placed in the pattern space. Another buffer is provided empty, at the start of each invocation, called the hold space. The x swaps the pattern space for the hold space.
s#^#sed -n 1p wifipass.txt this inserts another sed invocation into the empty hold space and evaluates it by the use of the e flag. The second invocation turns off implicit printing (-n option) and then prints line 1 of wifipass.txt only.
x the hold space is now swapped with the pattern space.Thus, line 1 of wifipass.txt is placed in the hold space.

10{G;s/\n(\S+).*/ \1/}

10{..} executes the commands between the ellipses on the tenth line of config.txt.
G append the contents of hold space to the pattern space using a newline as a separator.
s/\n(\S+).*/ \1/ match on the appended hold space and replace it by a space and the first column.

11{G;s/\n\S+//}

11{..} executes the commands between the ellipses on the eleventh line of config.txt.
G append the contents of hold space to the pattern space using a newline as a separator.
s/\n\S+// match on the appended hold space and remove the newline and the first column, thus leaving a space and the second column. 
