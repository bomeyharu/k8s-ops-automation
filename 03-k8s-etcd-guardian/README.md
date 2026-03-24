# K8s etcd Guardian & DR Automation (Python)
> Python SDKを用いて、etcdのバックアップ(DR)及び復旧するSRE Toolを実装するプロジェクト。

# Project Status
- [x] Phase 1 – Problem Definition & Design
- [ ] Phase 2 – etcd Health Check & Metrics Monitoring (Python SDK)
- [ ] Phase 3 – Snapshot Automation & Checksum Verification
- [ ] Phase 4 – Automated Multi-Storage Archiving (Bastion/Remote)
- [ ] Phase 5 – DR Guide(restore) & Fail-over Logic

# Project Overview
## ◼ Project概要
Kubernetes cluster上、すべての状態情報が格納される**etcd**は、システムの **「Single Source of Truth（唯一の真実のソース）」**です。
etcdのデータ消失はcluster全体の崩壊を意味するため、SREの観点から最も高い信頼性が要求される領域です。
本プロジェクトは、単なるバックアップにとどまらず、**データ整合性（Data Integrity）** の確保と**復旧時間（RTO）**の短縮を目的としています。
Pythonを用いてバックアップの全プロセスを制御し、障害発生時にオペレーターによる即時復旧が可能な環境を提供することを目標とします。

## ◼ Projectの重要観点
clusterの核心データであるetcdのバックアップを自動化し、バックアップファイルの容量やチェックサム（Checksum）を検証して **RPO** の信頼性を保証。 
- **Reliability（信頼性）**: バックアップ生成直後に Checksum検証を実行し、100%信頼可能なデータのみ保存。
- **RTO（復旧時間）の短縮**: 障害発生時のcluster状態を分析し、即時実行可能なetcdctl restoreガイドを提供。
- **Operational Efficiency（運用効率）の確保**: Retention Policyを通じて古いバックアップを自動整理し、ストレージ容量の圧迫を事前に防止。

## ◼ Design Strategy (Planned)
### 1. Robust Backup Pipeline (整合性中心の設計)
- **Fail-Fast バックアップ**: バックアップ開始前に etcd clusterの Health checkを行い、異常状態でのバックアップ生成を中断。
- **Checksum Verification**: etcdctl snapshot statusをPythonにてデータの整合性を検証し、成功時のみリモートストレージへアーカイブ。

### 2. Multi-Layer Storage & Retention (可用性の確保)
- **Tiered Storage**: 一次保存先としてローカル(Bastion)に保管し、データの即時利用性を確保。将来的には外部ストレージ(AWS S3)への転送を考慮した拡張可能な設計。
- **Auto-Rotation**: 設定された保存期限に従い、古いスナップショットを自動削除。手動管理によるToilを削減し、ストレージ容量圧迫のリスクを事前に防止。

### 3. Dynamic Disaster Recovery (災害復旧の自動化)
- **Recovery Guide**: バックアップ成功時、その時点の環境変数(Member ID, Endpoints等)を反映した復旧コマンドをログと共に自動生成。緊急時の判断コストを下げ、ヒューマンエラーを防止。 
[etcdctl backup guide](https://kubernetes.io/ja/docs/tasks/administer-cluster/configure-upgrade-etcd/#%E3%83%93%E3%83%AB%E3%83%88%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%8A%E3%83%83%E3%83%97%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88)
- **Monitoring Integration**: etcdのバックアップ成否及びメトリクスの構造化logging。clusterの安定性を把握し、信頼性の可視化を実装。

# Project Environment 
## ◼ Execution Environment

| Component | Version |
|-----------|----------|
| Host OS | Windows 11 / macOS |
| Hypervisor | VMware Workstation 17 |
| Guest OS | Rocky Linux 9.7 |
| Kubernetes | 1.30.9 |
| Ansible | 2.16 |
| Python | 3.12.12 (venv) |

### Execution Model
- Bastion上のPython venv環境で実行、またはK8s Jobとして実行。
- Kubernetes Python SDKを利用し、API Serverと直接通信。

### Notes / 備考
- VMware Workstation 17にてBastion及びKubernetes Clusterを構築。
- Bastion上のPython SDKにてK8S APIと通信。
- Kubernetes VersionはControl Plane・Worker Node同様。
- Python 3.12.12 venvを利用しモデュールをインストール 。

# Future Improvements
- バックアップ失敗時、Webhookによるリアルタイム障害アラート機能の追加。
- AWS S3へのバックアップも実装

# Validation & Results（Planned）
本番環境を想定した設計原則に基づき、検証環境にて実施予定です。
成功ケース及び失敗ケースの検証ログは  `./results/` に格納予定です。