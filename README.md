# Pokédex

A responsive web application that serves as a comprehensive Pokédex, built with Vue.js and Tailwind CSS.

<div style="display: flex; justify-content: space-around;">
    <img src="https://github.com/user-attachments/assets/9a7afd39-873c-41f8-8b1c-47aa88237346" alt="Image 1" style="width: 300px; height: auto; object-fit: cover;" />
    <img src="https://github.com/user-attachments/assets/7842b725-21c2-4d38-9874-90621dd6de47" alt="Image 2" style="width: 300px; height: auto; object-fit: cover;" />
    <img src="https://github.com/user-attachments/assets/75a150e3-6871-4419-899a-21b3832d02dd" alt="Image 3" style="width: 300px; height: auto; object-fit: cover;" />
</div>




## Features

- 🔍 Real-time Pokémon search
- 📊 Detailed stats, moves, and evolution chains
- 📱 Responsive design for all devices
- ✨ Modern UI with Pokédex-inspired theme
- 🚀 Performance optimized with data caching

## Quick Start

```bash
# Install dependencies
npm install

# Start application
npm run serve
```

## Tech Stack

- Vue.js
- Tailwind CSS
- Vue Router (for routing)
- PokéAPI
- Vue Composition API

## API Usage

This project uses the [PokéAPI](https://pokeapi.co/) to fetch Pokémon data. The application caches responses for better performance.

## Component Structure

```
components/
├── Pokedex.vue       # Main component
├── SearchPanel.vue   # Search and list functionality
└── DetailsPanel.vue  # Pokémon details and stats
```

