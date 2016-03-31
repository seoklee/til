# Java Random Stuff

---

## IO

Streams

- like a pipe
- more than just files
- can input from program to a file and vice versa.

Decorator Pattern

- basis for java I/O
- complicated, but simplifies
	- BufferReader wrapping reader
	- PrintWriter wrapping FileWriter

Hierarchy

- object
	- InputStream
	- OutputStream
	- Reader
	- Writer


~~~ java
...
//Reading
private String getTextFromFile() 
{
	FileReader reader = null
	String allText = "";
	BufferReader bReader = null;
	try {
		reader = new FileReader("text.txt");
		bReader = new BufferedReader(reader);
	
		string line;	
		while(( line = bReader.readline() ) != null)
		{
			allText += line + "\n";
		}
		
		return allText;
		
	} catch (FileNotFoundException e) {
		e.printStackTrace();	
	} catch (IOEception e) {
		e.printStackTrace();
	}
	finally
	{
		if(bReader != null) {
			try {
				reader.close();
			} catch (IOException e)
			{
				e.printStackTrace();
			}
		}
	}
}

//Writing
public void outputPage(int pageNubmer)
{
	PrintWriter writer = null;
	
	try 
	{
		writer = new PrintWriter(new FileWriter("outText.txt"));
		writer.println(pageMap.get(pageNumber).getText());
	} catch (IOException e) {
		e.printStackTracker();
	}
	finally
	{
		if( writer != null) {
			writer.close
		}
	}
}	

//Stream, wrap around reader like BufferedReader. You can also wrap a BufferedReader
CapitalizationReader extends BufferedReader {

	public CapitalizationReader(Reader in) 
	{
		super(in);
	}
	
	@Override
	public void readLine() throws IOException {
		string line = super.readline();
		if(line != null) 
			return line.toUpperCase();
			
		return null;
	}

}
~~~

File manipulation

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

