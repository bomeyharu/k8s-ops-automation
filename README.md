# K8s Platform Engineering & SRE Portfolio
本リポジトリは、実務で直面した運用課題を自動化により体系的に改善するためのエンジニアリングポートフォリオです。

仕事で経験した繰り返し作業(Toil)や手作業による運用リスクを低減するため、
IaCを活用した自動化の設計・実装プロセスを記録し、継続的に発展させることを目的としています。

## 1. Background（プロジェクト背景）
従来の手動運用において発生する**繰り返し作業**やそれに起因する**ヒューマンエラー**は、クラスタ停止（**Downtime**）の主要因となります。
本プロジェクトは、これらの運用リスクを自動化によって解決し、システムの **信頼性**と**運用効率の最大化**を実現することを目的としています。

# 2. Project Goals（プロジェクト目標）
- **安全性最優先（Safety-First）**: すべての自動化ロジックの前後に**二重Health Check**を配置し、クラスタの不整合状態での処理実行を防止。
- **実行環境の標準化と隔離**: **Ansible Execution Environment（EE）**やコンテナベースのRuntimeを導入し、実行環境の依存関係を排除し、どの環境においても同一の自動化結果を得られる再現性を確保。
- **運用自動化（Day-2 Ops）**: 単なる構築にとどまらず、**Self-Healing**や**証明書ライフサイクル管理** など、実運用フェーズにおける課題解決を目指す。

# 3. Roadmap（進行状況）
- [x] **Phase 1: K8s Hop-by-Hop Upgrade（Ansible）** （In Progress）
- [ ] **Phase 2: K8s Diagnosis-Tool（Python）** （Planned）
- [ ] **Phase 3: Multi-node Scale-out（Terraform）** （Coming Soon）
- [ ] **Phase 4: Automated Certificate Management（Python）** （Coming Soon）

# 4. Repository Structure
- **[01-k8s-hop-by-hop-upgrade/](./01-k8s-hop-by-hop-upgrade/)**: K8s Hop-by-Hop Upgrade (Ansible)
  - Ansibleを用いたサービス影響を最小化するアップグレード自動化
- **[02-k8s-diagnosis-tool/](./02-k8s-diagnosis-tool/)**: K8s SRE Diagnosis Toolkit (Python)
  - Python SDKを用いた障害検知及びロギング(Logging)
- **[03-k8s-scale-out-terraform/](./03-k8s-scale-out-terraform/)**: Multi-node Scale-out (Terraform)
  - Terraformを活用したインフラ拡張自動化
- **[04-ocp-cert-management/](./04-ocp-cert-management/)**: Automated Certificate Management (Python)
  - OCP証明書のライフサイクル自動管理

# 5. Tech Stack
| Category | Tools & Libraries |
| :--- | :--- |
| **Provisioning** | Terraform (v1.x) |
| **Configuration** | Ansible (v2.16+), Ansible EE |
| **Orchestration** | Kubernetes (v1.30+), kubeadm |
| **Scripting** | Python (v3.12+) |
| **Query & Analysis** | JMESPath (json_query), JSON/YAML |
| **Virtualization** | VMware Workstation 17, Podman/Docker |
