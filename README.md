# patent_sim
Use Gen AI and vector database to search for patent similiarities

This is an idea to utilize natural language processing to compare patents and patent ideas.  Utilizing the data from PatentsView, https://patentsview.org/download/data-download-tables, downloaded the title and abstract description of about 9 million patents (g_patent.tsv).  Took the information and passed through an embedding model to create the vectors.  Initially tried a couple different models from Hugging Face, with varying success, finally settling on Ollama 3.1.  This provides three different sizes of embeddings:  small (all-minilm, 384 dimensional vector), medium (nomic-embed-text, 768 dimensional), and large (mxbai-embed-large, 1024 dimensional).  On the embedding creation side (create_ollama_embedding.ipynb), looped through each patent title and abstract (separately), created the embedding, and wrote to a vector database.  Worked with Milvus vector database and the vector extended version of PostgreSQL.  These were a bit too heavy, so utilized chromedb, and also numpy arrays.  The numpy arrays made it simpler to perform the similarity testing (read_ollama_embeddings.ipynb) on multiple computers to get an understanding of the compute resources needed, and the time for execution.

On the creating embedding side, utilized a local Nvidia A2000 (6GB) GPU with a dual E5-2640v3 and 128GB of RAM, Windows 10.  An attempt was made with a virtualized (ProxMox) Ubuntu machine, which was not very successful.  Apparently, it is a known issue that Ollama virtualized does not utilize the GPU, thus the Windows machine.  Also, an i5-6500T, 64GB RAM machine was tested, with decent results.  For embedding a small number of items, say less than 1000, an i5 is decent, taking only a few minutes.  For larger number of patents, the GPU is necessary.

Emedding times on the GPU system was a few seconds for 1000 items, to a few to 20 hours for one million embeddings (small to large models), and for 9 million, the time was approximately 6 days of continual run time.  File size is around 6 GB per million large emeddings, with the 9 million being about 60GB.

Interestingly on the similarity testing side, the i5 machine take around 8 minutes per 1 million similarities, with only a 10% time difference between 368 dimensional and 1024 dimensional calculations.  Did not find a way to utilize the GPU to speed up similarity calculations, which is a subject for further exploration.
