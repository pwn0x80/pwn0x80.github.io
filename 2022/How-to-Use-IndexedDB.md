![image](https://user-images.githubusercontent.com/25504458/195301214-40e37d56-39da-40d2-b368-d6947236e364.png)


### A short but comprehensive guide to IndexedDB

IndexedDB is a standard for storing structured data on the client side. This article is a guide on the functional way to use application of this technology.

**Introduction**

This is a key/value store (NoSQL database), which is considered the ultimate solution for storing data in browsers. IndexedDB is an asynchronous API. This means that performing priority operations will not block the flow of the user interface. IndexedDB can store an indefinite amount of data, which depends on the user.

-   It is supported on all modern browsers.
-   It supports transactions, versioning, and provides good performance.

Inside the browser we can also use:

 — Cookies that may contain a small number of strings.

 — DOM storage (or Web storage) is a term that usually defines localStorage and sessionStorage as two key/value type stores. sessionStorage does not save data that is cleared after the session ends, but localStorage does.

 — Local/session storage has the disadvantage of limiting the available space: from 2 MB to 10 MB of space per site.

 — In the past, there was a Web SQL wrapper for SQLite. But now Web SQL is outdated and is not supported in some modern browsers. It has never been a generally accepted standard, so it should not be used, however, 83% of users still have this technology on their devices in accordance with Can I Use.

Although it is technically possible to create several databases for a site, one is usually created. You can create multiple repositories inside this database. The database is private for the domain, so one site cannot access the IndexedDB repositories of another.

  

#### Creating a Database

When starting with this indexedDB it is necessary to open a connection, then specify the Database Schema that it will have, this refers to the structure of the objects that will be stored, and finally, carry out the desired transactions.

One of the most noteworthy advantages of IndexedDB is that, being independent of whether there is internet connection or not, the application can work both **online** and **offline**.

  Working with the database begins with an open request:

` const request = indexedDB.open(DB_Name, 1, ()=>{});`  
The first two parameters are obvious. The third parameter, which is optional, is a callback that is called only if the version number is greater than the currently installed version of the database. In the body of the callback function, you can update the structure (stores and indexes) of the database.  
  
The object store is created or updated in the callback with: 
`db.createObjectStore(‘storeName’, options, ()=>{})` or we can implement in 
`request.onupgradeneeded =()=>{}`.

The **open** method returns an IDBOpenDBRequest object, in which we are interested in three event handlers:  

-   **onerror** ;
-   **onsuccess** ;
-   **onupgradeneeded** .

**Onerror** will be called in case of an error and will receive an error object in the parameters.  
  
**Onsuccess** will be called if everything went well, but the method will not receive an open database instance as a parameter. The open database is available from the request object: **request.result** .  
  
While everything was the same as before, but now the differences begin. The second argument to the **open** method is the version of the database. The version can only be a natural number. If you pass a fraction, it will be rounded up to an integer. If there is no database with the specified version, then **onupgradeneeded** will be called , in which you can modify the database if an old version exists, or create a database if it does not exist at all.
  ![image](https://user-images.githubusercontent.com/25504458/195291534-4a216eac-3524-4aec-9a28-559e94cde4b8.png)

  

The index gives you the ability to get the value by that particular key, and it must be unique (each element must have a unique key).
```javascript
const db = request.result;
const store = db.createObjectStore(Object_Store, { keyPath: “id” })
```

- Thus, a generic database connection function might look like this:
```javascript
let createIndexedDB = (indexedDB) => {
  return new Promise((resolve, reject) => {
    //db Name
    const request = indexedDB.open(DB_Name, 1);
    request.onerror = (err) => reject(err);
    request.onupgradeneeded = () => {
      const db = request.result;
      const store = db.createObjectStore(Object_Store, { keyPath: "id" })
      store.createIndex(name, [name], { unique: false });
    }
    request.onsuccess = () => {
      console.info("successfully open db")
      resolve(request.result);
    }
  })
}
```
unique specifies whether the index value must be unique and
no duplicate values ​​can be added.  
`store.createIndex(name, [name], { unique: false });`

###   Working with records
As mentioned in the introduction, any operations on records in IndexedDB occur within a transaction. A transaction is opened with the **transaction** method . In the method, you must specify which ObjectStore you need and the access mode: reading, reading and writing, version change. The upgrade mode is essentially the same as the onupgradeneeded method.  
  
I didn’t measure specific numbers, but I think from a performance point of view it’s better to carefully set transaction parameters: open only the ObjectStore you need and don’t ask for a write when you only need to read.

```javascript
let option = db => (method, arg, callback) => {

const transaction = db.transaction("Object_Store", method);

let store = transaction.objectStore("Object_Store")

return callback(arg, store)

}
```

```javascript
let modeOption = (option2func) => {

let getAlldb = (arg) => option2func(“readwrite”, arg, (arg, store) => store.getAll())

let dataViewdb = (arg) => option2func(“readonly”, arg, (arg, store) => store.getKey(arg))

let dataDeletedb = (arg) => option2func(“readwrite”, arg, (arg, store) => { store.delete(arg) })

let dataPutdb = (arg) => option2func(“readwrite”, arg, (arg, store) => { store.put(arg) })

return ({ dataViewdb, dataDeletedb, dataPutdb, getAlldb })
```  

to connect transactions (modeOption) and createIndexedDB we use custom promise base piping
  ```javascript
  
let pipe = (...functions) => (input) => func.reduce((next, func) =>
  next.then(func), Promise.resolve(input)
)
```
  

- Always check for browser support before using the IndexedDB API. You never know which browser the user is using:
```javascript

const indexedDB =

window.indexedDB ||

window.mozIndexedDB ||

window.webkitIndexedDB ||

window.msIndexedDB ||

window.shimIndexedDB;

let globalstore = indexedDB == null ?

console.log(“Broswer did not Support”)

:

pipe(

createIndexDB,

option,

modeOption

)(indexedDB)  

```

### Example-

https://github.com/pwn0x80/radio/blob/main/src/services/indexedDB.js

~pwn0x80 :)
