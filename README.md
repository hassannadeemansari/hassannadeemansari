name: Generate profile animations

on:
  schedule:
    - cron: "0 0 * * *"   # once a day
  workflow_dispatch:        # lets you trigger it manually from the Actions tab
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  snake:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate contribution snake SVGs
        uses: Platane/snk@v3
        with:
          github_user_name: hassannadeemansari
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark

      - name: Push snake SVGs to the "output" branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  contrib-3d:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Generate 3D contribution graph
        uses: yoshi389111/github-profile-3d-contrib@0.7.1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          username: hassannadeemansari

      - name: Commit and push
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -f profile-3d-contrib/*.svg
          git commit -m "Update 3D contribution graph" || echo "Nothing to commit"
          git push
