# File structure of the project

`` `
project
│ README.md
│ ...
│
We──webpack // webpack configs
│ │ ...
│
└──docs // documentation for front and api servers
│ │ ...
│
└──src // app
   │ index.ts // application entry point
   │
   └──assets
   │ │ ...
   │
   C──core // initialization files. Creating a store, initializing dependencies, mechanisms for connecting features to a store
   │ │ ...
   │
   Fe──feature // features
   │ └──authorization
   │ └──loadTenders
   │ └── ...
   │
   Mod──modules // modules
   │ └──Main
   │ └──Profile
   │ └──Tenders
   │ └── ...
   │
   Ser──services // services
   │ └──api
   │ └──config
   │ └─ii18n
   │ └── ...
   │
   ───shared // types, helpers, basic react-components, fonts, ...
      Ty─tytypes
      └──helpers
      View──view
      └── ...
`` `