# File manipulation

- Path class

~~~ java
Path path = Paths.get("~/home/lee/hi.txt");

//kinda like touch command in linux
try {
	Files.createFile(path);
} catch (IOException e) {
	e.printStackTrace();
}

try {
	Files.deleteIfExists(path);
	// this will throw an excption if file does not exists
	//Files.delete(path)
	
} catch (IOException e) {
	e.printStackTrace();
}

try {
	Files.move(path, Paths.get("~/home/");
} catch (IOException e) {
	e.printStackTrace()
}
~~~
