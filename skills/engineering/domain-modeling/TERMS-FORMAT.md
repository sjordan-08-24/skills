# Term file formats

Route each resolved term to one of two files, then write its entry in that file's schema.

## Choose the file

- **`glossary.yaml` takes a domain term:** a concept unique to this project. Test: does the concept exist outside this project? If it does not, it belongs here.
- **`ubiquitous-language.yaml` takes general developer vocabulary that needs a ruling.** Make each entry one of two rulings:
  - **Synonym ruling:** several words name one concept. One word wins; list the others under `rejected`.
  - **Boundary ruling:** words routinely conflated name different concepts. Draw the line under `distinct_from`, and reject nothing.
- **Leave a general concept that needs no ruling out of both files.**

## glossary.yaml

```yaml
terms:
  - term: Order
    definition: >
      A customer's request to buy one or more products, from placement until
      it is fulfilled or cancelled.
    identity: orders table; the Order aggregate in src/ordering
    invariants:
      - an Order holds at least one line
      - a cancelled Order never ships
    enforced_at: [db, ci]
    rejected:
      purchase: "names the payment, not the request"
      transaction: "collides with a database transaction"
    note: >
      Billing raises an Invoice after delivery; an Order never carries payment state.
```

| Field | Required | Holds |
|---|---|---|
| `term` | yes | The canonical word. |
| `definition` | yes | What the concept is, in one or two sentences. |
| `identity` | no | Where the term shows up concretely: a directory, table, or type. |
| `invariants` | no | Rules that always hold for the concept. |
| `enforced_at` | no | Where the invariants are checked, such as `db` or `ci`. |
| `rejected` | no | A map of each losing word to the reason it loses. |
| `aka` | no | An industry synonym acceptable in external docs and ADRs. |
| `examples` | no | Concrete instances. |
| `note` | no | Usage guidance that is not a definition. |

## ubiquitous-language.yaml

```yaml
terms:
  - term: directory
    layer: filesystem
    definition: >
      A filesystem container holding files and other directories. The node,
      not the string that names it.
    aka: "folder (GUI contexts and end-user documentation only)"
    rejected:
      folder: "GUI metaphor; ambiguous in a terminal, an agent prompt, or code"
    distinct_from:
      path: "the directory is the container; the path is the string naming it"
```

| Field | Required | Holds |
|---|---|---|
| `term` | yes | The canonical word. |
| `layer` | yes | One of `filesystem`, `git`, `code`, `runtime`, or `data`. |
| `definition` | yes | What the word names, in one or two sentences. |
| `identity` | no | Where the term shows up concretely, when that is not obvious. |
| `examples` | no | Concrete instances. |
| `aka` | no | An industry synonym acceptable in external docs and ADRs. |
| `rejected` | no | A map of each losing word to the reason it loses. |
| `distinct_from` | no | A map of each confusable word to the actual difference. |
| `note` | no | Usage guidance that is not a definition. |

## Rules

- **Give every rejected word its reason.** A rejected word with no reason gets re-proposed six months later.
- **Define what the concept is, in one or two sentences.** Record what it does under `invariants` or `note`.
- **Update an existing entry rather than adding a second one.** Each concept has exactly one entry across both files.
