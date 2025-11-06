# Transform commit message's
```js
const commits = $input.all();
const allCommits = []

for (const commit of commits) {
    allCommits.push(commit.json.commit.message)
}
const allCommitsText = allCommits.join("\n\n")

return [{
    json: {
        commits: allCommitsText
    }
}];
```

# Fetch Latest Release

```js
const releases = $input.all().map(item => item.json);
// Vind de release met de nieuwste published_at datum
const latest = releases.sort((a, b) => new Date(b.published_at) - new Date(a.published_at))[0];

return {
    json: {
        name: latest.name, 
        tag: latest.tag_name, 
        published_at: latest.published_at, 
        body: latest.body, 
        url: latest.html_url
    }
};
```

# LLM Chain Prompt

```text
Can you generate release notes from these commit messages? Discard "Merge Pull Requests". Dont assume a version number, just the plain text. Summarize the notes in concise Release Notes

{{ $json.commits }}

Each commit message should be on a new line using \\n\\n and start with a hypen. Don't answer anything else then the generated text.
```

# Send message and wait for response

```text
What would the new release version be?

Latest release: {{ $json.tag }}
Release notes:  

{{ $json.output.message }}
```