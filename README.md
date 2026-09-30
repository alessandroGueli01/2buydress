# 2buydress - Marketplace per Moda Second-Hand

Un marketplace moderno per comprare e vendere vestiti second-hand, costruito con Node.js, Express, SQLite e Bootstrap 5.

##  Video Demo
https://youtu.be/hEh9ad0FxKI

---

## Scelte di Progettazione

### Architettura
Il progetto segue un'architettura **Client-Server** disaccoppiata (REST API):
- **Backend (API REST)**: Gestisce la logica di business, l'accesso ai dati e l'autenticazione. Espone endpoint JSON consumati dal frontend.
- **Frontend (SPA)**: Una Single Page Application sviluppata in React che gestisce la presentazione e l'interazione utente, comunicando con il backend tramite chiamate asincrone (fetch).

### Database
È stato scelto **SQLite** gestito tramite l'ORM **Sequelize**.
- **Motivazione**: SQLite offre un database relazionale completo senza la necessità di configurare server esterni (come MySQL o PostgreSQL), rendendo il progetto estremamente portabile e facile da valutare.
- **Struttura**: Il DB è normalizzato con relazioni chiave:
  - *User* 1:N *Listing* (Venditore -> Annunci)
  - *User* N:M *Listing* (Preferiti)
  - *Listing* 1:N *Conversation* (Chat contestuali all'annuncio)

### Interfaccia Utente (UI/UX)
- **Framework**: **Bootstrap 5** è stato utilizzato per garantire un design responsive e moderno con tempi di sviluppo ridotti.
- **Componenti**: L'interfaccia è divisa in componenti React riutilizzabili (Navbar, ListingCard, ChatWindow) 
## 🛠 Scelte Implementative e Funzionali

### 1. Autenticazione e Sicurezza
- Utilizzo di **JWT (JSON Web Tokens)** per un'autenticazione stateless.
- Le password vengono hashate con **bcryptjs** prima del salvataggio.
- Middleware dedicato (`auth.js`) per proteggere le rotte private.

### 2. Gestione Immagini
- Per semplicità implementativa e per evitare dipendenze da servizi cloud esterni (es. AWS S3), le immagini vengono caricate localmente tramite **Multer** nella cartella `uploads/`.
- I percorsi vengono salvati nel DB e serviti staticamente da Express.

### 3. Sistema di Chat
- Implementata come risorsa REST nidificata. Una conversazione nasce sempre in relazione a un annuncio specifico, facilitando il contesto della compravendita.

### 4. Gestione Ordini (Simulazione)
- Il flusso di acquisto è simulato: l'utente può procedere al checkout, inserire i dati di spedizione e l'ordine viene registrato nel database, aggiornando lo stato dell'annuncio a "Sold" (Venduto).

## 📁 Struttura Progetto

```
2buydress/
├── backend/
│   ├── server.js              # Server Express principale
│   ├── config/
│   │   └── database.js        # Configurazione SQLite/Sequelize
│   ├── models/
│   │   ├── User.js            # Modelli Sequelize...
│   │   ├── Listing.js         # Modello annunci
│   │   ├── Conversation.js    # Modello conversazioni
│   │   └── Message.js         # Modello messaggi
│   ├── routes/
│   │   ├── auth.js            # Login/registrazione
│   │   ├── listings.js        # CRUD annunci
│   │   ├── users.js           # Gestione utenti
│   │   └── chat.js            # Messaggistica
│   │   ├── orders.js          # Gestione ordini e spedizioni
│   │   ├── reviews.js         # Recensioni utenti
│   │   ├── admin.js           # Dashboard amministratore
│   │   ├── reports.js         # Segnalazioni
│   │   └── notifications.js   # Notifiche
│   └── middleware/
│       └── auth.js            # Autenticazione JWT
├── frontend/src/
│       ├── components/        # Componenti React riutilizzabili
│       ├── context/           # React Context (es. AuthContext)
│       ├── pages/             # Componenti che rappresentano le pagine
│       └── App.js             # Componente root e routing
├── .env.example
└── package.json
```

## 🛠️ Installazione e Avvio

### Prerequisiti
Per far funzionare il progetto sono necessari:
- Node.js versione 20 o superiore
- npm
- un terminale bash/zsh

### 1. Clonare il progetto
```bash
git clone <repository-url>
cd 2buydress
```

### 2. Installare le dipendenze
```bash
npm install
```

### 3. Configurare le variabili d'ambiente
Crea un file `.env` nella cartella principale del progetto e inserisci almeno queste variabili:

```env
JWT_SECRET=changeme
FRONTEND_URL=http://localhost:3000
PORT=3000
```

Se il file `.env.example` è presente, puoi copiarlo così:

```bash
cp .env.example .env
```

### 4. Avviare il database
Il progetto usa SQLite tramite Sequelize. Il database viene creato automaticamente al primo avvio.

### 5. Avviare il server
Per avviare l'applicazione in modalità sviluppo:

```bash
npm run dev
```

Oppure in modalità produzione:

```bash
npm start
```

### 6. Aprire l'applicazione
Dopo l'avvio, apri nel browser:

```text
http://localhost:3000
```

### 7. Eseguire i test
Per verificare il corretto funzionamento del progetto:

```bash
npm test -- --runInBand
```

### 8. Popolare i dati di esempio (opzionale)
Se vuoi usare dati iniziali di esempio:

```bash
npm run seed
```

Il seed ora crea solo utenti demo (non crea annunci, ordini o recensioni).

Nota: le immagini degli annunci non vengono generate automaticamente. Le foto vanno caricate manualmente quando crei o modifichi un annuncio.

Limite annunci: ogni utente può avere al massimo 5 annunci attivi contemporaneamente.

### 9. Test manuali da eseguire
Per verificare che tutto funzioni correttamente, segui questi passaggi:

1. Apri la home page e verifica che compaiano annunci.
2. Registrati con un nuovo utente e fai login.
3. Crea un annuncio da utente autenticato.
4. Apri il dettaglio di un annuncio e verifica che le immagini e i dati siano corretti.
5. Aggiungi un annuncio ai preferiti e controlla che appaia nella pagina dedicata.
6. Controlla che la navbar mostri il badge notifiche quando ci sono notifiche.
7. Esegui il checkout di un annuncio e verifica che l’ordine venga registrato.
8. Esegui i test automatici con:

```bash
npm test -- --runInBand
```

## 🔧 Script Disponibili

- `npm start` - Avvia il server in produzione
- `npm run dev` - Avvia il server in modalità sviluppo con nodemon

##  API Endpoints

### Autenticazione
- `POST /api/auth/register` - Registrazione utente
- `POST /api/auth/login` - Login utente
- `GET /api/auth/me` - Dati utente corrente

### Listing (Annunci)
- `GET /api/listings` - Lista annunci con filtri
- `GET /api/listings/:id` - Dettaglio annuncio
- `POST /api/listings` - Crea nuovo annuncio
- `PUT /api/listings/:id` - Aggiorna annuncio
- `DELETE /api/listings/:id` - Elimina annuncio
- `POST /api/listings/:id/favorite` - Toggle preferiti

### Utenti
- `GET /api/users/:id` - Profilo utente
- `PUT /api/users/profile` - Aggiorna profilo
- `GET /api/users/:id/listings` - Listing di un utente

### Chat
- `GET /api/chat/conversations` - Conversazioni utente
- `POST /api/chat/conversations` - Crea conversazione
- `GET /api/chat/conversations/:id/messages` - Messaggi conversazione
- `POST /api/chat/conversations/:id/messages` - Invia messaggio

##  Frontend

Il frontend è una **Single Page Application (SPA)** sviluppata in **React**.

### Pagine Principali (Componenti)
- **HomePage**: Catalogo prodotti con ricerca e filtri.
- **ListingDetailPage**: Dettaglio annuncio.
- **NewListingPage**: Form per creare/modificare annunci.
- **ProfilePage**: Dashboard utente.
- **ChatPage**: Messaggistica in tempo reale.
- **LoginPage / RegisterPage**: Autenticazione.

### Tecnologie Frontend
- **React** - Libreria UI
- **Bootstrap 5** - Framework CSS
- **React Router** - Navigazione
- **Context API** - Gestione stato globale (Auth)

## Database

### Modelli Sequelize

#### User
```javascript
{
  name: String,
  email: String (unique),
  password: String (hashed), // Non esposto via API
  bio: String,
  location: String,
  avatar: String,
  rating: Number,
  reviewCount: Number,
  isVerified: Boolean,
}
```

#### Listing
```javascript
{
  title: String,
  description: String,
  price: Integer, // In centesimi
  category: String,
  brand: String,
  size: String,
  condition: String,
  photos: JSON, // Array di URL
  sellerId: Integer, // Foreign Key a User
  status: String,
  views: Number,
  attributes: JSON
}
```

#### Conversation
```javascript
{
  listingId: Integer, // Foreign Key a Listing
  lastMessageAt: Date, // Per ordinamento
  status: String
}
```

#### Message
```javascript
{
  conversation: ObjectId (ref: Conversation),
  sender: ObjectId (ref: User),
  content: String,
  type: String,
  isRead: Boolean
}
```

## Sicurezza

- **JWT Authentication** - Token-based auth
- **Password Hashing** - bcryptjs
- **Input Validation** - Mongoose validators
- **CORS** - Cross-origin resource sharing
- **Rate Limiting** - (da implementare)

## Utenti
 Mario Rossi
 mario.rossi@example.com
 Mario123!
//se al momento non si vedono le foto, fate npm run seed, create l'utente che può comprare e vendere//
admin
testuser1@example.com
password123

# 2buydress
