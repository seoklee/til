JPA Notes 

@OneToOne Relationship and @OneToMany Relationships

- the object that is 'Many' side, or you can think the object that HAS 'One' side as the variable, has to declare @JoinColumn
    - for an example. Company has List<Branch> and Branch has Company. Then the Branch object has to throw in @JoinColumn annotation.
        - the default 'name' parameter in @JoinColumn is...
            - The name of the relationship attribute in the source entity (From previous example, it would be 'company') + an underscore + the name of the primary key on the target entity
            - Ex. 'company_id' or "COMPANY_ID" _they are case insensitive_
    - For the convenience sake, the object that has @ManyToOne is called the owner isde. (Because they have the 'One' object)
    - The other side of relationship, which is 'One' side that has 'Many' variables, does mappedBy. 

- When it's un-natural to have one variable in the object to represent a primary key, consider using composite primary key using EmbeddedID

        