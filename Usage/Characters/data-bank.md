---
order: character-50
route: /usage/core-concepts/data-bank
tags:
    [
        vector storage,
        RAG,
        retrieval-augmented generation,
        vectors,
        documents,
        files,
        attachments,
    ]
---

# Data Bank (RAG)

Retrieval-augmented generation (RAG) is a technique for providing external sources of knowledge to the LLM. It helps improve the accuracy of AI answers by accessing information outside of the model's training data.

SillyTavern provides a set of tools for building a multi-purpose knowledge base from a diverse number of sources, as well as using the collected data in LLM prompts.

## Accessing the Data Bank

The built-in Chat Attachments extension (included by default in release versions >= 1.12.0) adds a new option in the "Magic Wand" menu - Data Bank. This is your hub for managing the documents available for RAG in SillyTavern.

## About Documents

Data Bank stores file attachments, also known as documents. The documents are divided into three scopes of availability.

1. Global attachments - available in every chat, either solo or group.
2. Character attachments - available only for the currently chosen character, including when they are replying in a group. _Attachments are saved locally and are not exported with the character card!_
3. Chat attachments - available only in the currently open chat. Every character in the chat can pull from it.

!!! info Note
While not formally a part of the data bank, you can attach files even to individual messages. Use the Attach File option from the "Wand" menu, or a paperclip icon in the message actions row.
!!!

What can be a document? Practically anything that is representable in plain text form!

Examples include, but are not limited to:

-   Local files (books, scientific papers, etc.)
-   Web pages (Wikipedia, articles, news)
-   Video transcripts

Various extensions and plugins can also provide new ways to gather and process data, more on that below.

## Data Sources

To add a document to any of the scopes, click "Add" and pick one of the available sources.

### Notepad

Create a text file from scratch, or edit an existing attachment.

### File

Upload a file from the hard drive of your computer. SillyTavern provides built-in converters for popular file formats:

-   PDF (text only)
-   HTML
-   Markdown
-   ePUB
-   TXT

You can also attach any text files with non-standard extensions, such as JSON, YAML, source codes, etc. If there are no known conversions from the type of a selected file, and the file can't be parsed as a plain text document, the file upload will be rejected, meaning that raw binary files are not allowed.

!!! info Note
Importing Microsoft Office (DOCX, PPTX, XLSX) and LibreOffice documents (ODT, ODP, ODS) requires a [Server Plugin](https://github.com/SillyTavern/SillyTavern-Office-Parser) to be installed and loaded. See the plugin's README page for installation instructions.
!!!

### Web

Scrape text from a web page by its URL. The HTML document is then processed through the [Readability](https://github.com/mozilla/readability) library to extract only usable text.

Some web servers may reject fetch requests, be protected by Cloudflare, or rely heavily on JavaScript to function. If you're facing issues with any particular site, download the page manually through the web browser and attach it using the file uploader.

### YouTube

Download a transcript of the YouTube video by its ID or URL, either uploaded by the creator or automatically generated by Google. Some videos may have the transcripts disabled, also parsing of age-restricted videos is unavailable as it requires login.

The script is loaded in the video's default language. Optionally, you can specify the two-letter language code to try and fetch the transcript in a specific language. This feature is not always available and may fail, so use it with caution.

### Web Search

!!! info Note
This source requires to have a [Web Search](/extensions/WebSearch.md) extension installed and properly configured. See the linked page for more details.
!!!

Perform a web search and download the text from the search result pages. This is similar to the Web source but fully automated. A chosen search engine will be inherited from the extension settings, so set it up in advance.

To begin, specify the search query, max number of links to be visited, and the output type: one combined file (formatted according to the extension rules) or an individual file for every single page. You can choose to save the page snippets as well.

### Fandom

!!! info Note
This source requires to have a [Server Plugin](https://github.com/SillyTavern/SillyTavern-Fandom-Scraper) installed and loaded. See the plugin's README page for installation instructions.
!!!

Scrape articles from a [Fandom](https://www.fandom.com/) wiki by its ID or URL. As some wikis are very large, it may be beneficial to limit the scope using the filter regular expression, it will be tested against the article's title. If no filter is provided, then all of the pages are subject to be exported. You may save them either as individual files for every page, or joint into a single document.

### Bronie Parser Extension (Third-Party)

!!! warning Note
This source comes from a third-party and is **not affiliated** with the SillyTavern team. This source requires you to have Bronya Rand's [Bronie Parser Extension](https://github.com/Bronya-Rand/Bronie-Parser-Extension) installed as well as Server Plugins that require the parser to work.
!!!

Bronya Rand's Bronie Parser Extension allows the use of third-party scrapers, such as miHoYo/HoYoverse's [HoYoLab](https://wiki.hoyolab.com) into SillyTavern, similar to the other data sources.

Currently, Bronya Rand's Bronie Parser Extension supports the following:

-   miHoYo/HoYoverse's HoYoLab (for Genshin Impact/Honkai: Star Rail) via [HoYoWiki-Scraper-TS](https://github.com/Bronya-Rand/HoYoWiki-Scraper-TS)

To begin, install Bronya Rand's Bronie Parser Extension by following it's [installation guide](https://github.com/Bronya-Rand/Bronie-Parser-Extension?tab=readme-ov-file#installation) and install a supported Server Plugin into SillyTavern. Restart SillyTavern and go to the _Data Bank_ menu. Click `+ Add` and you should see that your recently installed scrapers are added into the possible list of sources to obtain information from.

## Vector Storage

So, you've built yourself a nice and comprehensive library of information on your specific subject matter. What's next?

To use the documents for RAG, you need to use a compatible extension that will insert related data into the LLM prompt.

Vector Storage, which comes bundled with SillyTavern, is a reference implementation of such an extension. It uses embeddings (also known as vectors) to search for documents that relate to your ongoing chats.

!!! info Fun facts

1. Embeddings are arrays of numbers that abstractly represent a piece of text, produced by specialized language models. More similar texts have a shorter distance between their respective vectors.
2. Vector Storage extension uses the [Vectra](https://github.com/Stevenic/vectra) library to keep track of file embeddings. They are stored in JSON files in the `/vectors` folder of your user data directory. Every document is internally represented by its own index/collection file.
   !!!

As the Vectors functionality is disabled by default, you need to open the extensions panel ("Stacked Cubes" icon on the top bar), then navigate to the "Vector Storage" section, and tick the "Enabled for files" checkbox under the "File vectorization settings".

By itself, Vector Storage does not produce any vectors, you need to use a compatible embedding provider.

## Vector Providers

### Local

These sources are free and unlimited and use your CPU/GPU to calculate embeddings.

1. Local (Transformers) - runs on a Node server. SillyTavern will automatically download a compatible model in ONNX format from HuggingFace. Default model: [jina-embeddings-v2-base-en](https://huggingface.co/Cohee/jina-embeddings-v2-base-en).
2. Extras - runs under the [Extras API](https://github.com/SillyTavern/SillyTavern-extras) using the SentenceTransformers loader. Default model: [all-mpnet-base-v2](https://huggingface.co/sentence-transformers/all-mpnet-base-v2).
3. Ollama - get it from <https://ollama.com/>. Set the API URL in the API connection menu (under Text Completion, default: `http://localhost:11434`). Must download a compatible model first, then set its name in the extension settings. Example model: [mxbai-embed-large](https://ollama.com/library/mxbai-embed-large). Optionally, check an option to keep the model loaded in memory.
4. llama.cpp server - get it from [ggerganov/llama.cpp](https://github.com/ggerganov/llama.cpp) and run the server executable with `--embedding` flag. Load compatible GGUF embedding models from HuggingFace, for example, [nomic-ai/nomic-embed-text-v1.5-GGUF](https://huggingface.co/nomic-ai/nomic-embed-text-v1.5-GGUF).
5. vLLM - get it from [vllm-project/vllm](https://github.com/vllm-project/vllm). Set the API URL and API key in the API connection menu first.

### API sources

All these sources require an API key of the respective service and usually have a usage cost, but generally calculating embeddings is pretty cheap.

1. OpenAI
2. Cohere
3. Google MakerSuite
4. TogetherAI
5. MistralAI
6. NomicAI

!!! warning Warning
Embeddings are only usable when they are retrieved using the same model that generated them. When changing an embedding model or source, it is recommended to purge and recalculate file vectors.
!!!

## Vectorization Settings

After you've selected your embedding provider, don't forget to configure other settings that will define the rules for processing and retrieving documents.

!!! info Note
Splitting, vectorization, and retrieval of information from the attachments take some time. While the initial ingestion of the file may take a while, the RAG search queries are usually fast enough not to create a significant lag.
!!!

### Message attachments

These settings control the files that are attached directly to the messages.

The following rules apply:

1. Only messages that fit in the LLM context window can have their attachments retrieved.
2. When the vector storage extension is disabled, file attachments and their accompanying message are fully inserted into the prompt.
3. When file vectorization is enabled, then the file will be split into chunks and only the most relevant pieces will be inserted, saving the context space and allowing the model to stay focused.

-   Size threshold (KB) - sets a chunking splitting threshold. Only the files larger than the specified size will be split.
-   Chunk size (chars) - sets the target size of an individual chunk (in textual characters, not model tokens!).
-   Chunk overlap (%) - sets the percentage of a chunk size that will be shared between adjacent chunks. This allows for a smoother transition between the chunks, but may also introduce some redundancy.
-   Retrieve chunks - sets the maximum amount of the most relevant file chunks to be retrieved. They will be inserted in their original order.

### Data Bank files

These settings control how the Data Bank documents are handled.

The following rules apply:

1. When file vectorization is disabled, the Data Bank is not used.
2. Otherwise, all available documents from the current scope (see above) are considered for the query. Only the most relevant chunks across all the files are retrieved. Multiple chunks of the same file are inserted in their original order.
3. The inserted chunks will reserve a part of the context before fitting the chat messages.

-   Size threshold (KB) - sets a chunking splitting threshold. Only the files larger than the specified size will be split.
-   Chunk size (chars) - sets the target size of an individual chunk (in textual characters, not model tokens!).
-   Chunk overlap (%) - sets the percentage of a chunk size that will be shared between adjacent chunks. This allows for a smoother transition between the chunks, but may also introduce some redundancy.
-   Retrieve chunks - sets the maximum amount of the file chunks to be retrieved. This allowance is shared between all files.
-   Injection Template - defines how the retrieved information will be inserted into the prompt. You can use a special \{\{text\}\} macro to specify the position of the retrieved text, as well as any other macros.
-   Injection Position - sets where to insert the prompt injection. The same rules as for Author's Note and World Info apply.

### Shared settings

-   Query messages - how many of the latest chat messages will be used for querying document chunks.
-   Score threshold - adjust to allow culling the retrieval of chunks based on their relevance score (0 - no match at all, 1 - perfect match). Higher values allow for more accurate retrieval and prevent completely random information from entering the context. Sane values are in a range between 0.2 (more loose) and 0.5 (more focused).
-   Include in World Info Scanning - check if you want the injected content to activate lore book entries.
-   Vectorize All - forcibly ingests the embeddings for all unprocessed files.
-   Purge Vectors - clears the file embeddings, allowing to recalculate their vectors.

!!! info Note
For "Chat vectorization" settings see [Chat Vectorization](/extensions/Chat-vectorization.md).
!!!

## Conclusion

Congratulations! Your chatting experience is now enhanced with the power of RAG. Its capabilities are only limited by your imagination. As always, don't be afraid to experiment!