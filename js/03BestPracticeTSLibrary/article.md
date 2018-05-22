Au cours du développement de nos projets, on peut se retrouver à écrire du comportement générique, susceptible d'être réutilisé autre part. Mais on est peut être en train de réinventer la roue si ce comportement est déjà distribué publiquement... Si ce n'est pas le cas, on pourrait être tenté de le mettre à disposition.
Nous allons voir ensemble la procédure et les bonnes pratiques de publication d'une bibliothèque Open Source écrit en TypeScript.

### Initialisation
Si nous partons de cette structure de projet:
- node_modules
- package.json
- lib
- test
avec un tsconfig.base.json qui mutualisera les options communes à la config de build et à celle pour le développement

### Packager notre projet pour NPM
Cette partie va répondre à "Que fournissons-nous à l'utilisateur". Pour éviter que le `node_module` de nos utilisateurs se transforme en monstre, il faut rester soucieux de n'embarquer que le nécessaire :
 - le package.json
 - la lib
 - les types

Pour cacher le superflus de NPM, créons un fichier `.npmignore` et mettons-y :
  - les sources TypeScript
  - les fichiers de config
  - les tests
  - la doc
Sans ce fichier, NPM va par défaut se calquer sur le `.gitignore`, mais les deux usages étant vraiment séparés, ça ne suffira pas. À noter aussi que certains fichiers sont ignorés implicitement, tel que node_modules.

### L'étape de build
Contrairement à un projet destiné à être directement exécuté par le navigateur, nous n'avons pas besoin de beaucoup modifier notre code source.
Bundlelifier son code ? Si nos utilisateurs ne veulent qu'une partie de notre bibliothèque, l'ensemble serait malgré tout importé, ce qui aurait un impact très négatif sur la taille de leur distribuable. Il vaut mieux laisser notre lib modulaire pour laisser le choix à l'utilisateur de prendre tout ou partie.
Minimifier son code ? Notre projet sera déjà servi sur NPM gzipé. Il vaut mieux garder le code clair pour l'utilisateur. Il s'y retrouvera beaucoup plus simplement s'il doit examiner une pile d'appel avec des noms de fonctions non minimifié !
Si notre bibliothèque est utilisé dans un projet Web, il est de leur responsabilité d'embarque une étape de build pour au moins élaguer le code mort et minimifier le reste.

Ajoutons la config TS pour générer ce build:
````json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "rootDir": "lib",
    "outDir": "dist"
  },
  "include": [
    "lib/*"
  ],
  "exclude": [
    "node_modules",
    "es",
    "dist",
    "build"
  ]
}
````


### Tester son packaging localement

## Votre projet sur NPM
Si vous n'avez pas de compte NPM, il va être temps d'en créer un ! Ensuite vous aurez le choix de publier directement via ce compte ou via une organisation rattaché à ce compte. Publier via une organisation permet d'avoir des packages NPM scopés, et de les regrouper (comme avec @angular par exemple).