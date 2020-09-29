# Εισαγωγή

Το gitlab προσφέρει χώρο, ώστε να αποθηκεύονται images, ακριβώς όπως στο docker hub, με τη διαφορά οτι αυτά τα image μπορούν να παράγονται αυτόματα σε κάθε push που γίνεται στο repository. Πρακτικά, τα images αύτα τρέχουν το application.

Για παράδείγμα, αν έχουμε ένα απλό project σε node.js με την ακόλουθη δομή

```markdown
- backend
- fonts
- app.js
- .
- .
- .
- Dockerfile
- .
- .
```

τότε μπορούμε να φτιάξουμε ένα Image με την εντολή

```
docker build -t imageName .
```

και μπορούμε να το κάνουμε run 

```
docker run -p 8080:8080 imageName
```

# Δημιουργία Repo στο  gitlab

Ας υποθέσουμε οτι το παραπάνω project το ανεβάζουμε στο gitlab. Κάθε φορά που κάνει κάποιος μια αλλαγή και την κάνει push, όλοι οι υπόλοιποι θα πρέπει να κάνουν pull τις αλλαγές και να φτιάχνουν τοπικά το image και να δοκιμάζουν την εφαρμογή αυτή. Επίσης, δεν υπάρχει κάποιος ελεγχος αν αυτές οι αλλάγες είναι σωστές, δηλαδή δεν υπάρχει κάποιο testing.

# Αρχείo .gitlab-ci.yml

Το gitlab προσφέρει τη δυνατότητα να αυτοματοποιήσουμε την παραπάνω διαδικασία. Το μόνο αρχεία που σχετίζεται με την αυτοματοποίηση αυτή λέγεται `.gitlab-ci.yml`. 
Μέσα σε αυτό καθορίζουμε τι ενεργείες θέλουμε να γίνονται κάθε φορά που γίνεται ένα push. 

# Αυτοματοποιημένη δημιουργία image

Συγκεκριμένα για την αυτοματοποίηση της δημιουργίας image το αρχείο `.gitlab-ci.yml` περιλαμβάνει τα εξής

```
build:
  image: docker:19.03.12
  stage: build
  services:
    - docker:19.03.12-dind

  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $CI_REGISTRY/giorgos.padelhs/test/image:latest .
    - docker push $CI_REGISTRY/giorgos.padelhs/test/image:latest
```

όπου του λέμε ποιο docker image να χρησιμοποιησει ώστε να τρέξουμε τα script που φαίνονται στη συνέχεια. Του λέμε ποιο stage είναι. Εδω είναι στο stage του build, κανονικά μπορεί να υπάρχουν και άλλα stages όπως `test,deployment κλπ`.
Στη συνέχεια στο `script` section ορίζουμε τη scripts θα τρέξουν στο docker που φτιάξαμε πιο πανω.
```markdown
- login
- build
- push
```

Πρώτα κάνουμε login με τα credentials στο registry του gitlab. 
Κάνουμε build το Image με tag latest
Kαι στη συνέχεια το κάνουμε push στο Registry του gitlab

# Image run τοπικά

Για να το τρέξει ο οποιοδήποτε το image με τις τελευταιες αλλαγές απλά πρέπει να παέι στο section  `Container Regisrty` και να κάνει copy to όνομα του image, να το κάνει pull και μετα run και τέλος!
Για παράδειγμα:
```
docker pull registry.gitlab.com/giorgos.padelhs/test/image
docker run -p 8080:8080 registry.gitlab.com/giorgos.padelhs/test/image

```
