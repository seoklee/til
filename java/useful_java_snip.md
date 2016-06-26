#Useful java code snip

## Fill copies in the list

~~~ java
List<Integer> freq = new ArrayList<Integer>(Collections.nCopies(10, 0));
~~~

## Reverse ordered collection

~~~ java
Set<Integer> s = new TreeSet<Integer>(Collections.reverseOrder());
~~~

## Comparator w/ Lambda

~~~ java
PriorityQueue<Map.Entry<Integer, Integer>> pq 
	= new PriorityQueue<>((o1, o2) -> o1.getValue() - o2.getValue());
~~~

## Anoynmous List?

~~~ java
List<String> list = Arrays.asList("one", "two", "three");
~~~
This is the easiest. But that list is immutable afterward (or at least does not
support add() or remove() ). So you can wrap it around the ArrayList constructor

new ArrayList<String>(Arrays.asList("one", "two", "three))

## Useful function calls

Character.isLetterOrDigit(char)
