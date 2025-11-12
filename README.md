# Devbox Setup

[![CI](https://github.com/neco-nico/ansible-devbox-setup/workflows/CI/badge.svg)](https://github.com/neco-nico/ansible-devbox-setup/actions)
[![Molecule Tests](https://github.com/neco-nico/ansible-devbox-setup/workflows/CI/badge.svg?label=Molecule%20Tests)](https://github.com/neco-nico/ansible-devbox-setup/actions)

🚀 **macOS開発環境を一括でセットアップするAnsibleプレイブック**

## クイックスタート

```bash
git clone https://github.com/neco-nico/ansible-devbox-setup.git
cd ansible-devbox-setup

# テスト実行後にセットアップ
make setup

# または直接実行（Python 3が必要）
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/setup.yml
```

## インストールされるツール

- **Homebrew** - macOS用パッケージマネージャー
- **開発ツール**: git, tig, wget, gh (GitHub CLI), fzf, ripgrep
- **Node.js環境**: volta (Node.jsバージョン管理), Node.js, yarn
- **その他**: Ansible, Git用のzshエイリアス

## 主要機能

- ✅ **冪等性**: 何度実行しても安全
- ✅ **Dry Run**: 事前に変更内容を確認可能 (`--check`)  
- ✅ **自動テスト**: Moleculeテストでセットアップ結果を自動検証

## 🎬 デモ

| セットアップ実行例 | 冪等性確認例 |
| --- | --- |
| ![Setup Demo Placeholder](docs/images/setup-demo_1.gif) | ![Idempotence Demo Placeholder](docs/images/dempotence-demo.gif) |

## 開発・テストコマンド

### Makeコマンド一覧
```bash
make help           # ヘルプ表示
make test           # 全テスト実行（lint + syntax + check）
make setup          # テスト後にセットアップ実行
```

### 個別テスト
```bash
make linting        # YAML・Ansible Lint
make syntax         # 構文チェック  
make check          # Dry run（事前確認）
```
 
## 📊 パフォーマンス（参考値）

| 項目 | 所要時間 | 備考 |
| --- | --- | --- |
| 初回セットアップ | 41.813秒 | ネットワーク速度や既存インストール状況に依存 |
| 再実行（冪等性確認） | 18.558秒 | 変更がない場合は短時間で完了 |

## ファイル構成

```
ansible-devbox-setup/
├── site.yml               # Ansibleエントリーポイント
├── playbooks/             # Ansibleプレイブック
│   └── setup.yml          # メインセットアッププレイブック
├── ansible.cfg            # Ansible設定
├── requirements.yml       # Ansible collections依存関係
├── roles/                 # Ansibleロール
│   ├── common/            # 共通設定（Homebrew、Ansible、開発ツール）
│   └── git/               # Git関連設定
└── docs/                  # 詳細ドキュメント
```

## 対象環境

- **macOS**: Apple Silicon (M1/M2/M3) または Intel
- **シェル**: zsh（macOSデフォルト）
- **前提条件**: Python 3（macOSに標準搭載）

## 🔒 セキュリティ

### 安全なインストール手順
- **curl|bash回避**: セキュリティのため `curl | bash` パターンは使用しません
- **パッケージマネージャー優先**: Homebrew等の署名済みパッケージを使用
- **チェックサム検証**: 外部バイナリはパッケージマネージャー経由で検証済み
- **非root実行**: 可能な限りユーザー権限で実行

### Ansibleセキュリティ機能
- **冪等性保証**: 何度実行しても副作用なし、安全な繰り返し実行
- **事前確認**: `make check` で実際の実行前にテスト可能
- **外部スクリプト排除**: 全ロジックがAnsibleプレイブック内に含有

### 機密データ保護
```bash
# 機密データにはAnsible Vaultを使用
ansible-vault create secret_vars.yml
ansible-vault edit secret_vars.yml

# Vault付きで実行
ansible-playbook playbooks/setup.yml --ask-vault-pass
```

### 実行前検証
```bash
# 実行前に変更内容を確認
make check    # Dry run で予定変更を表示

# セットアップ後の検証（Moleculeテスト）
cd roles/common && molecule test
cd roles/git && molecule test
```

### 冪等性の証明

2回目以降の再実行では、すべてのタスクが `changed: 0` となり、システム状態が変化しないことを確認できます。

```bash
$ ansible-playbook playbooks/setup.yml

PLAY RECAP ******************************************************************************************************
localhost                  : ok=25   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## 学習で利用した書籍
- [Ansible実践ガイド 第4版［基礎編］](https://www.amazon.co.jp/Ansible%E5%AE%9F%E8%B7%B5%E3%82%AC%E3%82%A4%E3%83%89-%E7%AC%AC4%E7%89%88%EF%BC%BB%E5%9F%BA%E7%A4%8E%E7%B7%A8%EF%BC%BD-impress-top-gear/dp/4295016810/ref=sr_1_2_sspa?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=QD67ZRBRZYOT&dib=eyJ2IjoiMSJ9.4REd3aaynFadfkN5wQRQH5n9wkvCubTbJr93YYVac63iBPWNFJAeCj12UiosDPqv2NW8ARMUayqlJi1ZBKlmjhT1yZ3v7eBqoUVqlUS3LkLVf2PInuCyxLmHHHVc4gkABUadyNqE7nmMDD5U4H0nIn76vEw-cqbOJupzbKpWG9WCiUpxFUbixx_54vLKqeI0i1b3oATfktBy9AuyqTzQ4JwPK1GxZcl7HiYtGm8xy5WNHKmgagaYD_8cgpWY_tlQaB6vWHfiQpn7FEb17Qn7pCE6VH6LHMzcueMIyULZHbc.zgJukY8ipO8aPRm8nfGNZKqvG4Ur1UNcmCYwU1XxYj0&dib_tag=se&keywords=ansible%E5%AE%9F%E8%B7%B5&qid=1762401482&sprefix=ansible%E5%AE%9F%E8%B7%B5%2Caps%2C169&sr=8-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
