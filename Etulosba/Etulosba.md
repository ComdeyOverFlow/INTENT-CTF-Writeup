# Chall Overview

Url: https://etulosba.chal.intentsummit.org/

File Attachments: [main.js](https://github.com/ComdeyOverFlow/INTENT-CTF/blob/main/main.js)

# Application Overview
There are two Folders Which is `/files/images/` and `/files/binary`. Need a javascipt basic knowledge. 

```
server.get("/files/images/:name", function (req, res) {
    if (req.params.name.indexOf(".") === -1) {
        return res.status(400).json({ error: "invalid file name" });
    }
        res.sendFile(__dirname + path.join("/files/images/", req.params.name));
});
```

As you can see, in line (11), there are request with get and go to `/files/images` and more important is `:name`, that mean we can input the name what we want, but if it is not exist, it will show error with (400) status code in json. BUt if our input file or filename is exist, it will return the real file name of it.

# Where is flag?
```
fs.writeFileSync(path.join(__dirname, "flag.name"), process.env.FLAG_NAME);
fs.writeFileSync(path.join("/tmp", process.env.FLAG_NAME), process.env.FLAG);
```
In, line (27), You can see that there is a env with named `flag.name` and `process.env.FLAG`. So we know our flag file name. Read to go right? Not actually. There is have another import line. Here it is,

```
server.get("/files/binary/:name", function (req, res) {
    if (req.params.name.indexOf(".") !== -1) {
        return res.status(400).json({ error: "invalid file name" });
    }

    res.sendFile(path.resolve(__dirname, "/files/binary/", req.params.name));
});
```

There is another dictionary or you can call folder called `/files/binary/` and of course `:name` for input. Really similar with above code. So, If our input filename is exist. it will return the file of it.

# Quick note
I said return in above. But i wrong a little bit because it will not return as text in website because he used the `sendFile()` function. so it will return as file.

# Solution idea

So now, solution is simple, just call the `flag.name` env in `/files/images/` folder and we will get the real file name of flag.

And we can get our real flag file from `/files/binary` folder. btw, i missed onething to tell that in the line (28) of main.js or you can see here.
`fs.writeFileSync(path.join("/tmp", process.env.FLAG_NAME), process.env.FLAG);`

As you can see he created the flag file in `/tmp` with `FLAG_NAME` env and also env `FLAG`. So by that, we can know that our real flag file is in `/tmp` folder.

# Solution

I send this request in browser with get method `/files/images/..%2F..%2Fflag.name` and from that i got a `flag.name` file that contains the real flag's file name. 
When we open the `flag.name`, we got the real flag's name called `imaflagimaflag`. (Nice flag name btw 😂)

And i send this request `/files/binary/%2ftmp%2fimaflagimaflag` which leads to real flag's file and we got back `_tmp_imaflagimaflag` file that have our real flag! Cool! `INTENT{absolutely_great_job_with_the_absolute_path}`.

Btw if you don't know why need to put `%2f`. It is just url encoding that mean `/` because we can't put `/` behind the `/files/images` folder because in main.js he just set our input to `:ourinput`. We can only put `:` not `/`. So, we can trick the website with url encoding wiht `%2f` leades to `/`. Cool! Right.

# References
1. https://www.geeksforgeeks.org/express-js-res-sendfile-function/
2. https://nodejs.org/api/path.html#pathresolvepaths
3. https://www.digitalocean.com/community/tutorials/nodejs-how-to-use__dirname
4. https://stackoverflow.com/questions/25463423/res-sendfile-absolute-path
5. https://www.tabnine.com/code/javascript/functions/express/Response/sendfile
6. https://www.digitalocean.com/community/tutorials/use-expressjs-to-deliver-html-files
7. https://www.geeksforgeeks.org/node-js-fs-writefilesync-method/
8. https://nodejs.org/api/fs.html
9. https://www.google.com/ (not troll 😂)
