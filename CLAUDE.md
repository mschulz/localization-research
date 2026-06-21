# Knowledge Base Instructions  
## Architecture  
- raw/ contains unprocessed source material. Never modify files here.  
- knowledge/ contains compiled articles. Only the LLM writes here.  
- outputs/ stores query results, reports, and deliverables.  
## Compilation Rules  
When compiling new sources from raw/:  
1. Read the master index at knowledge/_index.md first.  
2. If the file is a PDF file, convert the contents to markdown before processing it.
3. If the file is a scanned PDF, first search for a freely accessible version, and if that fails attempt to convert the contents to markdown before processing it.
4. For each new source, identify key concepts, entities, and claims.  
5. Check if articles already exist for these concepts in knowledge/.  
6. If yes, UPDATE the existing article with new information and cite the source.  
7. If no, CREATE a new article with a clear title and summary.  
8. Add backlinks between related articles using [[double brackets]].  
9. Preserve all references in each document so that deeper research can be conducted if required.
10. Indicate using a checkmark which references already in raw/ and have been processed
11. In reference-register, provide the link to the file mentioned in the Source heading to the paper in raw/.
12. Update knowledge/_index.md with any new articles or changed summaries.  
13. Add YAML frontmatter: tags, date-compiled, source-files, status.  
14. Flag contradictions explicitly: "Source A claims X, but Source B claims Y."  
## Health Check Rules  
When running a health check:  
1. Scan all articles for broken backlinks.  
2. Identify articles that reference sources no longer in raw/.  
3. Flag claims that lack source citations.  
4. Detect duplicate concepts that should be merged. 
5. Report concepts which are unique across all articles. 
6. Write a health report to outputs/health-check-YYYY-MM-DD.md.  
## Query Rules  
When answering questions against the knowledge base:  
1. Read knowledge/_index.md to orient yourself.  
2. Follow backlinks to gather relevant articles.  
3. Synthesize across multiple articles, not just one.  
4. Cite specific source files from raw/ for every factual claim.  
5. Save substantive query results to outputs/ for future reference.  
## Entity Types (customize for your domain)  
- Hardware: what hardware was used in the sensors, what medium was used (RF, light, sound, etc.). the underlying system/technology in use
- Software: is software freely available, what language was used
- Algorithm: what algorithms are used to process the data
- Accuracy: how accurate is the method, how does it compare to other methods
- Evaluation: the evaluation method for the proposed technique
- Ground Truth: the method of ground truth data gathering,
- Metrics: the applied metrics, 
- Baseline: whether the authors establish a baseline for their work.