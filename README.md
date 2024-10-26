# Modern Pokédex

A responsive web application that serves as a comprehensive Pokédex, built with Vue.js and Tailwind CSS.

<div style="display: flex; justify-content: space-around;">
    <img src="https://github.com/user-attachments/assets/89c7444c-48da-4a55-a217-7a2a97a7d7a6" alt="Image 1" style="width: 300px; height: auto; object-fit: cover;" />
    <img src="https://github.com/user-attachments/assets/f8d7492d-b43c-44bf-bc23-d1597ebfd685" alt="Image 2" style="width: 300px; height: auto; object-fit: cover;" />
    <img src="https://github.com/user-attachments/assets/d30cdc07-e8f8-4d2e-bd9f-2564f3247e5c" alt="Image 3" style="width: 300px; height: auto; object-fit: cover;" />
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

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

MIT License - see LICENSE.md for details.
