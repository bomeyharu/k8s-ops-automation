# K8s Diagnosis Tool (Python)
> Python SDKを用いて、K8s上のアプリケーション(Pod)の障害原因特定・ロギングするSRE Toolを実装するプロジェクト。

# Project Status
- [ ] Phase 1 – Problem Definition & Design (Observability Focus)
- [ ] Phase 2 – Pod Log & Event Analyzer (Python SDK)
- [ ] Phase 3 – Node Impact Analysis Logic
- [ ] Phase 4 – Integrated Reporting & etcd Backup

# Project Overview
## ◼ Project概要
Kubernetes標準のSelf-healing機能（Pod再起動など）は、ワークロードの可用性を維持しますが、
**なぜ障害が発生したのか**という根本原因分析（RCA）までは提供しません。
通常、オペレーターは「kubectl logs」や「kubectl describe」を用いて手動でトラブルシューティングを行いますが、コンテナの再起動（CrashLoopBackOff）が繰り返される場合、
過去のログが消失し、原因特定が困難になる課題があります。
本プロジェクトは、オペレーターおよび開発者の視点から**障害の早期検知**及び**影響範囲の可視化**を自動化し、クラスター全般のObservabilityを向上させることを目的としたプロジェクトです。

## ◼ Projectの重要観点
Python SDKを活用し、分散したクラスター情報を統合・分析することで、運用監視の高度化を実現する。
- **RCAの迅速化**: ログの自動アーカイブにより、Pod再起動時のログ消失を防止、調査時間を約50%短縮する。
- **Observabilityの向上**: Node障害とワークロードへの影響相関を自動抽出し、インフラの状態を定量化。
- **Data Integrity（データ整合性）の確保**: etcdバックアップの自動化により、災害復旧（DR）を確保.

## ◼ Design Strategy (Planned)
### 1. Application-Aware & Persistence Diagnosis (RCA迅速化への対応)
- **ログのアーカイブ化**: `CrashLoopBackOff`や`OOMKilled`発生時、Python SDKが即時に**Previous Logs（再起動前のログ）** 及びイベントをキャプチャして保存し、ログ消失を防止。
- **エラーパターンマッチング**: ログ内の特定キーワード（Connection Timeout, OutOfMemory等）を検知してキーワードを元に障害原因を自動分類・タグ付けし、オペレーターの分析リソースを最小化。

### 2. Service-Centric Impact Analysis (Observability向上への対応)
- **影響範囲の特定**: Node障害（NotReady）検知時、該当Node上で動作していたPodリストを抽出し、各Podの正常性をチェック。
- **ワークロード稼働率の算出**: 該当サービス（Deployment等）のDesired Replicas数に対し、現在「Running」状態のPod数を比較し、現在の稼働率（%）を算出し、レポート化する。

### 3. Data Integrity & DR Readiness (データ整合性確保への対応)
- **etcd snapshotの自動化**: クラスターの核心データであるetcdのバックアップを自動化し、バックアップファイルの容量やチェックサム（Checksum）を検証して **RPO** の信頼性を保証。 
[etcdctl backup guide](https://kubernetes.io/ja/docs/tasks/administer-cluster/configure-upgrade-etcd/#%E3%83%93%E3%83%AB%E3%83%88%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%8A%E3%83%83%E3%83%97%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88)
- **復旧ガイドの自動生成**: バックアップ成功時、その時点で実行可能な `etcdctl restore` コマンドをログと共に提供し、DR（災害復旧）状況下でのヒューマンエラーを防止。

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
- Webhookによるリアルタイム障害アラート機能の追加。

# Validation & Results（Planned）
本番環境を想定した設計原則に基づき、検証環境にて実施予定です。
成功ケース及び失敗ケースの検証ログは  `./results/` に格納予定です。