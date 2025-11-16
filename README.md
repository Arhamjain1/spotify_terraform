# Terraform Spotify Automation

This project uses **Terraform** and a **dockerized Spotify Auth Proxy** to automate the creation and management of Spotify playlists.

You can:

* Automatically create playlists
* Add / remove tracks
* Manage multiple playlists with code
* Version-control your entire Spotify setup

---

## 🚀 Features

* 🎧 **Create Spotify playlists with Terraform**
* 🔑 **Local OAuth via a Dockerized Spotify Auth Proxy**
* 🔐 **Keep secrets out of Git** (`.gitignore` recommended)
* 🛠️ **Reusable Terraform configuration**
* 🐳 **Works on Windows, macOS, and Linux**

---

## 📁 Project structure
```
Spotify_TF/
├── provider.tf      # Terraform provider configuration
├── playlist.tf      # Example playlist resource / module
├── .gitignore       # Prevents secrets from leaking
└── .env             # Spotify credentials (NOT committed)
```

---

## 🔧 Requirements

Before running the project, install:

* **Docker Desktop**
* **Terraform** (1.3+ recommended)
* **Git**
* A **Spotify Developer App**- create one at: [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)

---

## 🔑 Spotify Developer setup

1. Create a new Spotify App at the Spotify Developer Dashboard.
2. Open **Edit Settings → Redirect URIs**.
3. Add this **exact** redirect URI:

```
http://127.0.0.1:27228/spotify_callback
```

4. Save settings.
5. Copy the `Client ID` and `Client Secret`.
6. Put them in the `.env` file at the project root (do not commit `.env`):

```
SPOTIFY_CLIENT_ID=your_client_id
SPOTIFY_CLIENT_SECRET=your_client_secret
```

---

## 🧩 Run the Spotify Auth Proxy (dockerized)

Change into the proxy directory and build/run the container:

```bash
cd ./terraform-provider-spotify/
cd ./spotify_auth_proxy/
# build custom image
docker build -t spotify-auth-proxy-custom .
# run the proxy (it will listen on port 27228)
docker run --rm -it -p 27228:27228 --env-file .env spotify-auth-proxy-custom
#Edit the API key file and enter the api shown on your terimal there in double quotes.
```

When the container runs it will show an auth URL open it in your browser and complete the OAuth flow. The proxy exchanges tokens and exposes a local auth server that Terraform can use.

---

## 🧩 Terraform configuration

**provider.tf**

```hcl
terraform {
  required_providers {
    spotify = {
      source  = "conradludgate/spotify"
      version = "~> 0.4"
    }
  }
}

provider "spotify" {
  auth_server = "http://127.0.0.1:27228"
}
```

---

## 🎼 Example: create a playlist (playlist.tf)

```hcl
resource "spotify_playlist" "my_playlist" {
  name        = "My Terraform Playlist"
  description = "Playlist created through Terraform"
  public      = false
}
```

You can expand this with track resources, data sources, modules to manage multiple playlists, and variables for names/descriptions.

---

## ▶️ Running Terraform

Initialize the project:

```bash
terraform init
```

Plan the changes:

```bash
terraform plan
```

Apply the changes:

```bash
terraform apply
```

Terraform will create the playlist(s) in your Spotify account using the provider plus the local auth proxy.

---
