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
- ### Dockerfile
- .
- .
```
