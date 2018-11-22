# SPELL
## *"Sort-of Pseudocode" Everyday Logical Language*
SPELL is a pseudocode language created to maximize communication between technical and semi-technical individuals. It provides an open and expressive syntax that empowers the writer to convey clear, step-by-step information about a logical operation.

> The problem with most other pseudocode languages is that they are usually difficult for non-programmers to understand - either being too unstructured, or too complicated.    
>
> SPELL aims to be light-hearted and expressive, while still providing some consise structure where it is needed most to convey the details of a technical operation.

The language allows the writer to be as descriptive as needed to clearly convey operational intention, there are only a few keywords to remember and the syntax is simple and straight-forward.

Every SPELL operation (i.e. function, process, etc.) is written to include the `INPUTS`, followed by the `ACTION` steps of the process, and the `OUTPUT`. 

## Real World
SPELL is battle-tested in the field, and has a stable core syntax - although it may be refined over time with minor updates.

## Example
The following example describes a process that uses information from two web-service APIs, as well as a loaded customer profile to create an order for Toys starting with the letter "A". It then sends that order to another web-service API call to complete the entire operation.

```
((Create Order for Toys))
INPUTS
------
1. Toys = /api/toys (Web Service)
    WITH Authentication (Header)
2. Customer = Curstomer profile (Cache)
3. Shipper = /api/shipping (Web Service)
    WITH 
        - Authentication (Header)
        - Customer.UID (Parameter)
    WHERE    
        - Shipper.shippingMethod = truck    
        - AND Shipper.country = USA
        - AND Shipper is ((Valid Shipper))
4. Order = ((New Order))

ACTION
------
1. LOOP THROUGH Toys EACH CALLED v
    1. WHEN v:Name starts with an "A" 
        THEN 
            1. OrderProduct = ((New Order Product))
            2. OrderProduct:UPC = v:UPC
            3. Order:TotalPrice = Order:TotalPrice + v:Price
2. STOP LOOP
3. Order:ShippingCode = Order:Code + Shipper:Carrier Name + ((Current Date))
4. Order:ShippingPrice = ((Calculate Shipping))
5. Order:CustomerName = Customer:Name
6. Order:Address = Customer:Address
7. WHEN Shipper:ShippingPrice > 3.5   
        THEN Order:TotalPrice = Order:Price + Shipper:ShippingPrice    
        OTHERWISE Order:TotalPrice = Order:Price

OUTPUT
------
1. SEND Order TO /api/orders (Web Service)
```

## Syntax

### Schema
```
[(({Operation}))]
INPUTS
------
{1..x}. {Item} = {Detail} [(Type|Attribute[, ...n])]
    [WITH
        [- {Detail} [(Type|Attribute[, ...n])]]
        [...n] 
    ]
    [WHERE
        [- [...AND|OR ][{Item}[:{Item}...n] [comparison] [{Item}[:{Item}...n]|(({Operation}))]]]
        [...n]
    ]

ACTION
------
{1..x}.    
    [{Item}[:{Item}...n] = {Item}[:{Item}...n]|(({Operation}))]    
    [WHEN [{Item}[:{Item}...n] [comparison] {Item}[:{Item}...n]|(({Operation}))]
        [{1..x}. [...AND|OR ][{Item}[:{Item}...n] [comparison] {Item}[:{Item}...n]|(({Operation}))]]
        [...n]
        THEN [action]
            [{1..x}. action]
            [...n]
        [OTHERWISE [action]
            [{1..x}. action]
            [...n]
        ]
    ]

OUTPUT
------
{1..x}. SEND {Item}[:{Item}...n] TO [{Item}[:{Item}...n]|(({Operation}))|{Detail} [(Type|Attribute[, ...n])]]
    [WITH
        [- {Detail} [(Type|Attribute[, ...n])]]
        [...n] 
    ]
    [WHERE
        [- [...AND|OR ][{Item}[:{Item}...n] [comparison] [{Item}[:{Item}...n]|(({Operation}))]]]
        [...n]
    ]
```


### Conventions
- Use consistent indents, tabs or four (4) spaces for readability.
- If a statement is continued on a new line, indent that line from it's parent. Keep the indent level (after the initial indent) for continuations of descriptions, bulleted lists, or numbered lists.
    
    **Example**   
    ```
    1. WHEN ((Current Date)) is a Monday
        THEN <--Indent
            - Price = 2.5 <--Initial indent
            - Smiles = 5 <--No extra indent (bulleted list)
    2. Dog = "Cat" <-- Here is a long description, we're 
        always indenting the first continuation but will
        not consider changing the indent level until we 
        are completely done.
    3. WHEN ((Error))
        THEN
            1. Err = ((New Error Report)) <--Initial indent.
            2. Err.Code = 5294 <--No extra indent (numbered list)
            3. LOOP THROUGH Items
               1. Err.Cnt = Err.Cnt + 1 <--Indent (new numbered list)
    ```
- Sections should be underlined with hypens ("-").
- All keywords are upper-case, avoid upper-case for non-keywords to minimize confusion.
- Avoid using data types that wouldn't make sense to the laymen, for example:
    - Instead of "int" or "float", just say "integer" or "decimal".
    - Instead of "True" or "False", say "Yes" or "No", or "Enabled" or "Disabled".


## Keywords
- AND
- EACH CALLED
- EXIT
- LOOP THROUGH
- OR
- OTHERWISE
- STOP LOOP
- THEN
- WHEN
- WHERE
- WITH
- SEND
- TO

### Sections
- ACTION
- INPUTS
- OUTPUT

### Operators
- :    
    **Usage**: `{Item}[:{Item}...n]`    
    The colon ":" acts to indicate a child item (sometimes called "properties") of a parent-level item. Colons can be repeated to form a drill-down to a specific item.

    **Example**    
    Below, we indicate that on the "Order" item, there is a child-item "Price" which needs to be set to the value 3.14.
    ```
    1. Order:Price = 3.14
    ```

    **Example**    
    In the following example, we are indicating the "Won" item under the "Games" item, which is under the "Player" item, needs to be set to the value 56.
    ```
    1. Player:Games:Won = 56
    ```
- (( ))    
    **Usage**: `(({Operation}))`    
    Indicates an operation, such as a programmic function or process, or anoter SPELL script. This operator is optionally used to title each script (appearing on the line before the `INPUTS` statement).   
    `{Operation}` is the name of the operation.   

    **Example**    
    In the following example, the item "Orange" will be the result of the "New Fruit" operation.
    ```
    1. Orange = ((New Fruit))
    ```
- ( )    
    **Usage**: `({Type|Attribute[,...n]})`    
    Indicates one or more types or attributes of the item named in a "=" statement (optionally). The meaning of "type" and "attribute" here is used in a general sense, meaning it can be any noun or descriptive keyword(s) - such as a mode of transport, a static type, a style of carpetting, as long as it's short, and descriptive.
    `{Type|Attribute[, ...n]}` is one or more type/attribute name, seperated by a comma.    

    **Example**    
    In the following example, the `( )` indicates the "Lookups" item is from a "Web Service" type of data source, and uses a GET request method.
    ```
    1. Lookups = /api/lookups (Web Service, GET)
    ```
- <--    
    **Usage**: `<-- {Text}`    
    The arrow indicator is followed by text to describe the current statement or add additional comments or notes.

    **Example**    
    In this example we're commenting on a LOOP THROUGH statement to add clarity for our reader.
    ```
    1. LOOP THROUGH Orders EACH CALLED o <-- This means go through all the orders, Bob! 
    ```