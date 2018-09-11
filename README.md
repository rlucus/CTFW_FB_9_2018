# CTFW_FB_9_2018

# Kool-Story
Kool and HiZ Gang have been working on some new beatz, but they are afraid someone will steal their lyrics before they have a chance to lay down their new tracks.
They said they would be communicating at 34.217.107.134 on port 31000 but there does not seem to be anything there. 
Can you intercept their lyrics or is their disco funk secure?
------------------------------------------------------------------

So the first step was to listen to the port with netcat. 
`nc 34.217.107.134 31000`
This returns a string shortly followed by some random bytes and then disconnects. Repeated connections return different data after the introduction. Lets try logging the output.
`nc 34.217.107.134 31000 > log`

After examing this data, the first 2 bytes after the introduction of every transmission is `0x78 0x9c` and this is the file signature of zlib compression

Lets dig into that.
`nc 34.217.107.134 31000 | tail -n +2 | zlib-flate -uncompress > log`

Now we have a string that seems to be HEX. It's not ASCII or BASE64 though. 

Multiple interations of the previous command gives different data each time with no supossed pattern to the file size.

From here I spent a long time trying different things (single byte XOR, file signatures, encoding, ciphers) to no avail.

I then tried to XOR two of the smallest files together. This produced a long string of 0 at one point. So I found the string `2564716272656c656360237724756c4` This string occurs multiple times in all of the log files.

Further inspection reveals there is a string of 4086 char that starts with `a0e696167614023776` and appears in every transmission.

I tried all the previous tactics on this string. Also to no avail. At this point I decided to take the hint: 
`What is similiar between the transmissions? Why would something be the same in every transmission and what does this tell you about the type of communication?`
