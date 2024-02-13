# Getting Started With SQLite

## Installing

Mac and (most) Linux distributions come with SQLite3 already installed.

You can check for it by using the terminal

```bash
$ sqlite3 --version
```

For poor old Windows users, we need to get the binaries and command line tools, luckily its free.

[Download Here](https://www.sqlite.org/download.html)

In the "Precompiled Binaries for Windows" section, you need to download both

- 64-bit DLL (x64) for SQLite [version number]
- "A bundle of command-line tools for managing SQLite database files...."

Both are .zip files. Extract all files from both zips to the same directory, either:

- C:\sqlite3 (if your laptop is your own and you have access rights to the C:\ root)
- Your home folder (F:\ drive on University computers or C:\users\\[username-here])

(Note that on university computers your C:\users\\[username] folder might get erased periodically)

## Using SQLite3

- Open a terminal (either git-bash or cmd.exe)
- Navigate (cd) to where you unzipped the binaries
- Create a new database in a file called demo.db by using the commandline tool

```bash
$ sqlite3 demo.db
```

- Your prompt should have changed to the sqlite prompt

```bash
sqlite >
```

- You can now type sql statements which will be executed to modify the database stored in your demo.db file
- Each statement must end with a semicolon, if you forget, you will know because you will not see the sqlite prompt on the next line
- The main difference between sqlite and other database engines is that sqlite has a stripped down data-type set [which you can read about here](https://www.sqlite.org/datatype3.html)
- Create a basic table and insert some data

```sql
create table users (id integer primary key autoincrement, name text, location text);
insert into users (name, location) values ('Andy', 'Sheffield');
select * from users;
```

- You can safely exit sqlite at any point (ctrl + c a few times until you see the bash prompt) Your database will be saved in demo.db
- You can move your database to where you need it

```bash
mv demo.db /path/to/where/you/need/it
```

## Adding sqlite to PATH variable in Windows

- You can make it so you can run sqlite3 from any folder in your system instead of needing to navigate to the folder where you unzipped it everytime you want to use it.
- This is not really worth doing on the university computers because your configuration will get erased when you log out.
- [See here](https://dev.to/dendihandian/installing-sqlite3-in-windows-44eb#:~:text=Hit%20Windows%20key%20and%20find,Files%5Csqlite%20to%20the%20path.) for how to do this. Note that you need to replace C:\Program Files\sqlite to wherever you unzipped the commandline tools and binaries
