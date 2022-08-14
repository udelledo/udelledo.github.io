# Interactions

With the ability of writing and reading information persistently, it's time to review the sequence of an interaction
with the skill at the first invocation.

```mermaid
sequenceDiagram
    User->>Alexa:Invoke the skill
    activate Alexa
    Alexa ->> DynamoDB: Retrieve stored data
    DynamoDB -->> Alexa: no data available
    Alexa ->>+ Scraper: Parse theater
    Scraper -->>- Alexa: return ScrapedInfo
    Alexa ->> DynamoDB: Store
    DynamoDB -->> Alexa : 
    Alexa -->> User: Provide feeback
    deactivate Alexa
```

The above sequence occurs all within the single _Launch_ intent.

The current state of intents and their link is far too naive to be an effective interaction model, so it's time to draft a new version.

```mermaid
flowchart LR
    subgraph launch[Launch]
        activate(Open cinema \naround me)
    end
    user(User) -->|Activate\nthe skill| activate
    subgraph update[Update information]
        scrape(Scrape information)  
    end
    activate -.->|Action started\nautomatically\nif no information\n is available| scrape
    activate -->|Action on user request| scrape
    scrape-->movieNumber
    activate --> movieNumber
    subgraph listGraph[List movies]
        movieNumber{Are move then\n5 movies?} -->|no| list[[List all movies]]
        movieNumber -->|yes| filtering[[provide\nfiltering\noptions]]
    end
    subgraph goingToTheMovie
        movieWatched(Save watched\nmovie)
    end
    activate --> movieWatched
```