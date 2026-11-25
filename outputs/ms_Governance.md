# Governance Scan Questionnaire

## Application: ms - MS - Machine Learning & Multi-Agent Systems

## Link: https://github.com/johnzastrow/ms

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [ms.json](ms.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python with Jupyter (or Marimo), TensorFlow/Keras, kafka-python, numpy, matplotlib, pillow, tqdm, scikit-learn, seaborn, scipy, pandas, plus a local Apache Kafka 3.5.0 and ZooKeeper that the notebook downloads from archive.apache.org. There is no requirements file or other manifest; packages are installed by inline pip commands. RUN_ON_VPS.md documents an Ubuntu 24.04 JupyterHub setup.
2. **How are third-party API credentials and credentials managed?**
   No credentials, API keys or environment variables were found in the code. Kafka is used on localhost:9092 with no authentication configured.
3. **What have you done to make sure the application is secure?**
   The scan found no hardcoded secrets and no committed secret files. There are no web routes. No .gitignore exists and Kafka is plaintext and unauthenticated, so no specific hardening is evidenced.
4. **Are there areas of the application where additional development is planned?**
   The repo contains NEXT_STEPS.md, which the README describes as improvement plans (stage 1 and stage 2 notebooks implement some of them). Beyond that: Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   The README states the project explores whether multiple AI agents (CNN, ResNet, MobileNet) communicating over Kafka, with a Judge agent, classify CIFAR-10 images more accurately than a single model. It is an ML/agentic-AI learning project; business problem: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The README lists objectives and goals (Kafka pub/sub agents, model comparison, Judge soft-voting ensemble over 500 test images, extensible agents).
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. No end-user audience is stated; no routes or user interface exist beyond notebooks.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A local Apache Kafka broker (localhost:9092, plaintext, no credentials), archive.apache.org (download of the Kafka tarball), the CIFAR-10 public dataset download through keras, and Google Colab Drive paths (/content/drive/MyDrive) in one notebook. No credentials are used, so none are encrypted.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   The scan shows no data being sent out; all external access is download (read). No customer data or LLM prompts were observed. Colab Drive paths imply model files may be saved to Google Drive if run in Colab.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README.md, RUN_ON_VPS.md, MARIMO.md and NEXT_STEPS.md are present.
4. **Do you have any other concerns about the application?**
   RUN_ON_VPS.md documents JupyterHub bound to 0.0.0.0:8000 with a firewall rule to open the port. Notebooks download and run Kafka binaries without checksum verification. No dependency pinning or .gitignore.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | CIFAR-10 via keras.datasets.cifar10.load_data (all notebooks) | 32x32 images, 10 class labels | read | None observed |
   | Kafka broker localhost:9092 (stage2_image_class.ipynb and others) | JSON messages with image data and predictions | read_write | None observed (no auth) |
   | archive.apache.org Kafka 3.5.0 tarball (image_class.ipynb) | n/a | read | None observed |
   | classification_results.csv (stage2_image_class.ipynb) | prediction results | write | None observed |
   | Saved .keras model files (all notebooks) | n/a | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets or secret files found; no .gitignore present; git history has 13 commits and a remote on GitHub.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: notebooks use the Python logging module and write kafka.log and zookeeper.log files; no audit trail was assessed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: no routes, no auth, no database, no LLM calls, no dependency pinning.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: JupyterHub on 0.0.0.0:8000 per docs, unauthenticated plaintext Kafka, unverified binary downloads executed from notebooks, unpinned dependencies.
