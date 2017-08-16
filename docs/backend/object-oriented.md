#ASP.net


###Class

Properties

      public int CustomerId {get; set; }

Methode
      Funktioner. Actions / behaviour. Noget class'en kan.

###Object

Object er et instance af en class.


      new Customer();

      customer.Firstname = "Frodo";

### Entity

Hver enkel ting i classen.


Objekter der interagerer "cleanly" with one another.

Udtrække fælles elementer, for på den måde "DRY".

Genanvende. Nemmere at debugge.


### Field

Der holder data om objektet, i classen

  private string firstname;


Member virable - et andet ord for field.

### Property

Den offentlig tilgængelige af fields.

  public string firstname { get; set; }


Private fields står med småt, Public field står med stort.

### Types

Complex types - når du angiver en class som type.

### Methods/Funktioner

Skal representere en opførsel/handling.

    Noget classen kan.

    HentAlder(); //Get

    IndsætAlder(); //Set

### Constructor/Initialization

For at føre data ind i classen.

En constructor uden parameter er en *default constructor*.

God stil. - For at have en tom skabelon. Som parametrerne så får med.
