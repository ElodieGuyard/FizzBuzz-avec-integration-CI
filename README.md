# FizzBuzz-avec-integration-CI
Déployer une CI avec GitHub Actions sur un projet Node.js


## Créer un nouveau projet GitHub contenant un petit code JavaScript (Node.js), le **fizzbuzz.js** !
    

    ```jsx
    const fizzbuzz = (number) => {
      if (number % 3 == 0) {
        return "Fizz"
      }
      if (number === 5) {
        return "Buzz"
      }
      return number
    }
    
    module.exports = { fizzbuzz }
    ```

    On peut considérer ce dernier comme votre projet. L’objectif est de tester le **fizzbuzz** afin de confirmer qu’il correspond bien à notre spécification :
    
    - si le nombre est divisible par 3 : on écrit **Fizz**
    - si le nombre est divisible par 5 : on écrit **Buzz**
    - si le nombre est divisible par 3 et par 5 : on écrit **Fizzbuzz**
    - sinon : on écrit le nombre
    
    Nous allons donc créer un autre fichier qui permet de tester notre fonction. Nous allons nommer ce fichier **fizzbuzz.test.js**. On va tenter de vérifier le premier objectif de notre spécification : “si le nombre est divisible par 3: on écrit **Fizz**”
    
    ```jsx
    const test = require('node:test');
    const assert = require('node:assert');
    const { fizzbuzz } = require('./fizzbuzz.js')
    
    test('fizzbuzz(3)', (t) => {
      assert.strictEqual(fizzbuzz(3), "Fizz");
    });
    ```
    
    Il nous faut aussi un `package.json` pour que `npm` puisse savoir exécuter et tester notre code :
    
    ```jsx
    {
      "main": "test.js",
      "scripts": {
        "test": "node --test"
      }
    }
    ```
    
    Il suffit de rajouter ces 3 fichiers dans votre dépôt GitHub. Vous pouvez localement lancer vos tests avec `npm test` mais nous allons directement passer à la mise en place d’une CI avec GitHub Actions.
    
## Mettre en place une CI avec GitHub Actions
    
    - La CI n’est qu’une suite d’instructions que doit faire un ordinateur. Si ce dernier arrive à faire toutes les instructions sans qu’il n’y ait une erreur, alors la CI vous informera que le code est bon. Cela veut surtout dire qu’une CI **dépend** du projet. Les instructions pour un projet Node.js ne sont pas les mêmes que pour un projet Python par exemple.
    
    - GitHub met à disposition **gratuitement** un moyen d’exécuter une CI sur vos projets. Cela consiste à rajouter un fichier `.github/workflows/test.yml` dans votre dépôt Git.
    
    - Ce dernier est une **description** des **instructions** que doit faire la CI afin de valider votre projet. En l’occurence, pour notre projet, cela consistera à:
    
    1) Installer Node.js (`actions/setup-node@v4`)
    
    2) Cloner notre dépôt Git (et ainsi avoir notre code) (`actions/checkout@v4`)
    
    3) Exécuter `npm test` afin d’exécuter nos tests
    
    On peut retrouver dans cette descriptions d’autres éléments tels que:
    
    - Quand exécuter la CI (sur tous nos commits ?, sur nos pull requests ?, etc.) (`push`, `pull_request`)
    - Le système que nous voulons utiliser (Windows, Ubuntu - `ubuntu-latest`, Mac, etc.)
    
    À vous de jouer !
    
## Créer une nouvelle Pull-Request pour tester la CI (`fizzbuzz(15)`?)
    
    - Afin de tester notre CI, nous allons tester un autre objectif de notre spécification, “si le nombre est divisible par 3 et par 5 : on écrit **Fizzbuzz”.** Nous allons donc créer une nouvelle branche (`git checkout -b fizzbuzz-15`) où nous allons rajouter un nouveau test dans notre fichier **fizzbuzz.test.js**:
    
    ```jsx
    test('fizzbuzz(15)', (t) => {
      assert.strictEqual(fizzbuzz(15), "Fizzbuzz");
    });
    ```
    
    - Nous allons ajouter ce code et en faire un nouveau commit dans notre branch `fizzbuzz-15` et nous allons proposer cet ajout via GitHub en créant une nouvelle Pull-Request.
    
    - Nous devrions voir alors en dessous de notre Pull-Request notre CI. Celle-ci s’exécute et nous pouvons suivre son exécution en cliquant sur **Détails**. On remarquera que notre CI a échoué !
    
    ![Screenshot 2024-09-08 173003.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/45a05f05-4d98-40a1-a4ea-e15814c5b043/6060921a-cf43-4fdd-b938-e9822b046f90/Screenshot_2024-09-08_173003.png)
    
    - En cliquant sur détail, une nouvelle page s’affiche avec tout ce qui a pu être exécuté par notre CI jusqu’à l’erreur fatidique:
    
    ```jsx
    # Subtest: fizzbuzz(15)
    not ok 2 - fizzbuzz(15)
      ---
      duration_ms: 1.342097
      location: '/home/runner/work/js-test/js-test/fizzbuzz.test.js:9:1'
      failureType: 'testCodeFailure'
      error: |-
        Expected values to be strictly equal:
        + actual - expected
        
        + 'Fizz'
        - 'Fizzbuzz'
               ^
      code: 'ERR_ASSERTION'
      name: 'AssertionError'
      expected: 'Fizzbuzz'
      actual: 'Fizz'
      operator: 'strictEqual'
      stack: |-
        TestContext.<anonymous> (/home/runner/work/js-test/js-test/fizzbuzz.test.js:10:10)
        Test.runInAsyncScope (node:async_hooks:206:9)
        Test.run (node:internal/test_runner/test:789:25)
        Test.processPendingSubtests (node:internal/test_runner/test:517:18)
        Test.postRun (node:internal/test_runner/test:885:19)
        Test.run (node:internal/test_runner/test:831:12)
        async Test.processPendingSubtests (node:internal/test_runner/test:517:7)
      ...
    ```
    
    - Il semble que notre fonction `fizzbuzz` ne respecte pas notre spécification, nous devrions retourner **Fizzbuzz** mais nous ne retournons que **Fizz** ! À toi de faire en sorte à ce que la CI devienne verte et à corriger la fonction `fizzbuzz` - n’oublie pas que tu peux tester localement ton code avec `npm test`.
    
    - En entreprise, tant que cette croix sera là, la Pull-Request ne pourra pas être *mergée*. En effet, une Pull-Request n’est acceptée que si elle *passe* la CI.
    
    - Essaye donc de modifier ta Pull-Request afin que la CI soit *contente* 😁 !