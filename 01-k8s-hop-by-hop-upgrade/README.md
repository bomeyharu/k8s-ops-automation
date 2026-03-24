# K8s Hop-by-Hop Upgrade（Ansible）
> ETCD Quorumを維持しながら安全なKubernetesクラスタアップグレードを実現する自動化プロジェクト。

# Project Status
- [x] Phase 1 – Design & Validation
- [x] Phase 2 – Cluster Health Check
- [x] Phase 3 – Control Plane Automation (In-progress)
- [ ] Phase 4 – Worker Node Automation
- [ ] Phase 5 – Integration & Stress Testing

# Project Overview
## ◼ Project概要
本プロジェクトは、Ansibleを活用しKubernetesクラスタの
Hop-by-Hop Upgradeを実現することを目的とした自動化プロジェクトです。

Kubernetesクラスタの手動アップグレードでは、
ヒューマンエラーやETCD Quorum損失によるダウンタイムリスクが存在します。

これらのリスクを最小化するため、
Control PlaneのETCD Quorumを常時維持しながら、
サービス影響を考慮したHop-by-Hopアップグレード戦略を採用しています。

## ◼ Projectの重要観点
Ansible Playbookを活用し、アップグレード作業の標準化及び効率化を実現する。
- 作業時間を約60%短縮。
- Control Plane / Worker Node両方を対象とする。

## ◼ Design Strategy
- **単一マスター構成への対応**: 現時点での検証環境は単一Control Plane構成であるため、再起動時に API サーバーが一時的に中断されます。この制約を考慮し、API 内部状態の`Ready`チェックの代わりに **ポート稼働確認（wait_for: 6443）** を優先的に検証するロジックを採用しました。
- **今後の拡張計画**: **Phase 3 (Scale-out)** の完了後、マルチマスター環境における精緻な **ETCD Quorum（正足数）チェックおよび高可用性検証ロジック** を追加実装する予定です。

### 1. Safety-First Strategy (Hop-by-Hop)
- **Service Continuity**: `ansible serial: 1`を適用し、ノード１台ずつアップグレード実施。
- **Quorum Preservation**: Control Plane アップグレードの際、ETCD Quorumを維持。
- **Workload Protection**: Worker Node　アップグレードの際、`cordon / drain / uncordon`実施。

### 2. Pre/Post Validation (Double Health Check)
- **Fail-Fast Principle**: 作業前後にNode及びPodの状態をKubernetes API経由でチェックし、想定外の状態や異常を検知した場合、作業を中断。
- **Cluster Integrity**: `kubernetes.core` collectionを活用したCluster正常性チェック。

### 3. Human Error Mitigation
- **Centralized Management**: Bastion経由でのみ実行し、各Nodeへの直接ssh接続を排除。
- **Principle of Least Privilege**: 最小権限の原則に基づいた設計により、手動作業でオペレーターがRoot権限を取得することを防止。

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
- Bastion上のPython venv環境でAnsible実行。
- kubernetes.core collectionを利用。
- Control Plane / Worker Node同一Version構成。

### Notes / 備考
- VMware Workstation 17にてBastion及びKubernetes Clusterを構築。
- Bastion&AnsibleにてK8S Nodeを管理。
- Kubernetes VersionはControl Plane・Worker Node同様。
- Python 3.12.12 venvを利用しモデュールをインストール 。

# Future Improvements
- Ansible Execution Environmentへの移行。
- Checkpointを導入し、Upgrade中断・再開のケースでも問題なくPlaybookが実行できるように改善。

# Validation & Results（Planned）
本番環境を想定した設計原則に基づき、検証環境にて実施予定です。
成功ケース及び失敗ケースの検証ログは  `./results/` に格納予定です。