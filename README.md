#!/bin/bash

# Obtenemos la lista de repositorios
REPO_LIST=$(curl -s "https://api.github.com/users/Rootuser47363/repos?per_page=1000" | jq -r '.[].full_name')

# Recorremos la lista de repositorios
for REPO in $REPO_LIST; do
    # Obtenemos los stats del repositorio
    STATS=$(curl -s "https://api.github.com/repos/$REPO")

    # Obtenemos el número de estrellas
    STARS=$(echo "$STATS" | jq -r '.stargazers_count')

    # Obtenemos el número de forks
    FORKS=$(echo "$STATS" | jq -r '.forks_count')

    # Obtenemos el número de contribuidores
    CONTRIBUTORS=$(echo "$STATS" | jq -r '.contributors_url' | xargs curl -s | jq '. | length')

    # Obtenemos los lenguajes utilizados
    LANGUAGES=$(curl -s "https://api.github.com/repos/$REPO/languages" | jq -r 'keys[] as $k | "\($k): \(.[$k])"')

    # Escribimos los stats en el archivo README.md del repositorio
    echo "## Stats
- Stars: $STARS
- Forks: $FORKS
- Contributors: $CONTRIBUTORS
- Languages: $LANGUAGES" > "$REPO/README.md"
done
