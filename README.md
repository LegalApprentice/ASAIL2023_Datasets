# BVADatasets

This repo contains three BVA datasets, which are discussed in the paper by Vern R. Walker and Stephen R. Strong, "Toward Implementation Science: A Case Study Using LA-MPS to Research Argument Elements at Scale," in the Proceedings of the Sixth Workshop on Automated Semantic Analysis of Information in Legal Text (ASAIL 2023), June 23, 2023, Braga, Portugal.

## The FS-PTSD Dataset

To investigate the automatic labeling (“auto-enrichment”) of legal decisions at scale, we downloaded from the BVA website the first 10,003 decisions issued in 2018. We automatically converted these plain-text decisions to LSJson format (for explanation, see below and the paper referenced above), which resulted in a total of 1,360,230 sentences. We then used a pre-trained predictive model (a neural net model discussed in the reference below) to auto-enrich these sentences for 6 sentence types (rhetorical roles):

- Finding Sentences (primarily stating a finding of fact)
- Evidence Sentences (primarily stating the content of the testimony of a witness, stating the content of documents introduced into evidence, or describing other evidence)
- Reasoning Sentences (primarily reporting the trier of fact’s reasoning underlying the findings of fact, which often involves an assessment of the credibility and probative value of the evidence)
- Legal-Rule Sentences (primarily stating one or more legal rules in the abstract, without stating whether the conditions of the rule(s) are satisfied in the case being decided)
- Citation Sentences (referencing legal authorities or other materials, and usually containing standard notation that encodes useful information about the cited source)
- Other Sentences (not fitting into any of the previous 5 categories).

To create a manageable dataset for the case study described in the paper, we filtered the 10,003 auto-enriched decisions for Finding Sentences that contained the word “PTSD”, and we generated a count of the number of such sentences (“hits”) per decision. We then collected the set of all decisions that contained four or more such sentences (the number of hits occurs at the beginning of the filename). This resulted in the FS-PTSD Dataset (decisions = 449, sentences = 100,514).

These decisions are formatted in what we call Legal Semantic JSON (“LSJson”). This is a lightweight, extensible, data-exchange format for capturing the text and the semantic information associated with legal documents. LSJson stores the original decision string of characters, metadata about the decision, details about any predictive models used to auto-enrich the document, and added semantic information about the sentences and paragraphs of the decision. In particular, for the decision files in this dataset, the following semantic properties have been added through the auto-enrichment process (neural net model predictions):

- "enrichmentSet": the model predictions for sentence rhetorical role [NOTE: If the sentence is predicted to be a Finding Sentence, then there are also predictions of the sentence's linguistic "polarity" (related to, but distinct from, stance) and the sentence's "ruleID" (a method for identifying the topic or issue addressed by the sentence).]
- "forecast": the predicted classification, which could result from a threshhold formula, but in this dataset is equal to the classification with the highest model score
- "probability": the model scores associated with the forecast values

The neural net model we employed for auto-enrichment is described in: V. R. Walker, S. R. Strong, V. E. Walker, "Automating the classification of finding sentences for linguistic polarity," in the Proceedings of the 2020 Workshop on Automated Semantic Analysis of Information in Legal Text (ASAIL2020), 2020.

## The Stratified Random Sample (SRS) Dataset

To evaluate the accuracy of the auto-enrichment process for our use case, we drew a stratified random sample of 25 decisions (the “SRS Dataset,” consisting of 5,529 sentences) from the FS-PTSD Dataset, stratifying on the number of hits per decision. The SRS Dataset of 25 decisions was stratified as follows: 8 decisions with 4 hits (i.e., decisions containing 4 Finding Sentences predicted to contain the token “PTSD”), 6 decisions with 5 hits, 6 decisions with 6 or 7 hits, and 5 decisions with 8 or more hits.

To focus on the BVA’s reasoning, we evaluated the predictive accuracy of automatic sentence typing only for sentences in the analysis section of the BVA decisions (i.e., those sentences occurring within the document section headed as “REASONS AND BASES FOR FINDINGS AND CONCLUSIONS”). These document sections have no set internal structure, and they contain the rationale for any conclusions reached by the BVA. In total, there are 4,003 such sentences within the SRS Dataset.

## The Curated SRS Dataset

In reviewing the accuracy of the auto-enrichment of the 4,003 sentences in the REASONS-AND-BASES sections of the SRS Dataset, a practicing attorney with expertise in legal reasoning curated the labeling of those sentences. We then created a dataset with gold-standard labeling within those sections of the decisions. The frequencies of sentence types are: 246 Finding Sentences; 1,762 Evidence Sentences; 279 Reasoning Sentences; 616 Legal-Rule Sentences; 739 Citation Sentences; and 361 Other Sentences.

In addition to the LSJson properties described above, for sentences whose rhetorical role classification was corrected by the expert, the following JSON property was added:

- "labels": the corrected label assigned by the human expert
