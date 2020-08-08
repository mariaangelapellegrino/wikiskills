# The *Wikiskills* repository

This repository behaves as a support material for the paper entitled "Entity and Relation Linking Strategies for Vocal Assistants Question-Answering on Wikidata" submitted (and under evaluation) as a research paper at the Wikidata Workshop at ISWC 2020.

The Alexa skill presented in this repository has been implemented by a [generator](https://github.com/mariaangelapellegrino/virtual_assistant_generator) freely available on GitHub. 
It is a generator of Virtual Assistants (VA) extensions to reply natural language (NL) questions by querying SPARQL endpoints. The implemented mechanism to move from NL to SPARQL queries is knowledge graph (KG) independent, and it can be easily configured to query Wikidata. 

The file QALD-7 is the updated version of the original training set according to the WIkidata snapshot in July 2020.

## Dictionary-based WikiSkill
In its original version, the skill produced by the generator implements a dictionary-based solution where the pre-computed dictionary must be provided at the generator configuration time. The dictionary is both used to generate the interaction model, and it is stored in the back end to solve entities upon request. The dictionary deals with the entity linking (EL) and relation linking (RL) tasks. It has to provide the entity and predicate labels (as dictionary keys) and the related URL(s) (as a sorted list specified as dictionary values). Moreover, you can also specify a list of synonyms for each label.

The entity dictionary contains $2$K classes and $28.5$K resources, heterogeneous in the topic. In particular we retrieved $5$K \textit{people}, among actors, presidents, scientists; $3$K films, $2.5$K songs, and $3$K literary works belonging to the \textit{entertainment} category; $5$K among cities, countries and continents, and $2$K among rivers and mountains related to the \textit{geography} field; $4$K museums and monuments and $1.5$K artworks belonging to the \textit{art} field; $2.5$K among animals and celestial bodies, related to the \textit{scientific} field. The property dictionary contains all the used properties ($6.5$K). We lowercase all the labels and we remove all the labels contain unreadable codes. We add synonyms to entities and properties, by retrieving \texttt{also known as} from Wikidata pages.

The *dictionary-based skill* folder contains:
- the configuration file of the generator (conf.json);
- the produced skill (both the back-end that must be upload on AWS and the interaction model that must be upload on Alexa developer console);
- the results of the dictionary-based skill on the updated QALD-7 dataset.

## API-driven skill
The back-end produced by the generator provides the opportunity to modify the linking approach easily by affecting the script custom_function.js. Consequently, we left the intent implementation unchanged, by only affecting the linking and the SPARQL query running mechanism. 

We rely on [Wikibase-sdk](https://www.npmjs.com/package/wikibase-sdk), a library exposing functions to make read queries to a Wikibase instance API. It was primarily developed as wikidata-sdk, now generalized to support any Wikibase instance, wikidata.org among others.

Among the available functions, searchEntities enables the opportunity to perform EL by a label given as input. You can specify the maximum number of candidates and the library will return up to the desired limit. It can be used to solve both entities and properties (in the latter case, by specifying an extra parameter). 
We did not apply any further ranking approach. Any unlinkable mentions return empty list during the resolution phase.

Starting from the dictionary-based WikiSkill returned by the generator, we applied the following modifications to create the API-driven Wikiskill:
- we modified the *custom_functions.js* script to change the EL and RL method by substituting the dictionary lookup with the invocation of the *searchEntities* function;
- we modified the *custom_functions.js* script to change the SPARQL query execution by replacing the GET request with the \texttt{sparqlQuery} function invocation provided by the Wikibase-api to run SELECT and ASK queries;
- we deleted the dictionary file from the back-end;
- we added the sdk-wikibase node module.

The *API-driven skill* folder contains:
- the produced skill (both the back-end that must be upload on AWS and the interaction model that must be upload on Alexa developer console);
- the results of the dictionary-based skill on the updated QALD-7 dataset.
