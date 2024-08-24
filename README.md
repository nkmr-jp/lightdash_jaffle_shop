# dbt-coreとBigQueryで実行する手順

gcloud auth でのログイン。 (CloudSDKのインストールがまだの場合は [こちら](https://cloud.google.com/sdk/docs/install-sdk?hl=ja) を参考に設定。)
```sh
gcloud auth login
gcloud auth application-default login
```

## 各種インストール
```sh
python -m venv .venv
source .venv/bin/activate.fish
pip install -r requirements.txt
pip install dbt-bigquery
```

## テンプレートからプロファイルをコピー
```sh
cp profiles_template.yml profiles.yml
```

## profiles.yml を編集
`<your-project-id>`に自分のGoogleCloudのプロジェクトIDを書く。
```yml
project: <your-project-id>
```

## dbtのコマンド実行
```sh
dbt deps
dbt seed
dbt run
dbt docs generate
dbt docs serve
```

## lightdashをRenderにdeploy

[Render](https://render.com) のアカウントを作成してログイン。以下のボタンをクリックしてデプロイ。

<div>
<a href="https://render.com/deploy?repo=https://github.com/lightdash/lightdash-deploy-render">
  <img src="https://render.com/images/deploy-to-render-button.svg" alt="Deploy to Render">
</a>

See: https://github.com/lightdash/lightdash


## lightdashにデプロイ

```shell
npm install -g @lightdash/cli
lightdash login https://{{ lightdash_domain }} --token my-super-secret-token
lightdash dbt run
lightdash deploy --create
```

See: https://docs.lightdash.com/get-started/setup-lightdash/get-project-lightdash-ready

## サービスアカウントキーをlightdashに登録

以下の権限でサービスアカウントを作成。サービスアカウントキーを作成してlightdashに登録。

```shell
roles/bigquery.dataViewer
oles/bigquery.jobUser
```

See: https://docs.lightdash.com/get-started/setup-lightdash/connect-project#bigquery

## ligthdashでクエリ実行
![image](https://github.com/user-attachments/assets/825e9fe1-6bf6-4b64-bc7f-7a7c205f489c)



## How to use this project in Lightdash 

### Install Lightdash:
Take advantage of our installation script to easily run Lightdash locally. Check the [documentation page](https://docs.lightdash.com/get-started/setup-lightdash/install-lightdash#deploy-locally-with-our-installation-script) for more details.
```bash
git clone https://github.com/lightdash/lightdash
cd lightdash
./install.sh

# when prompted, select "fast" setup (option 1)
```

### dbt connection:

- type: github
- personal access token: (follow the [docs instructions](https://docs.lightdash.com/get-started/setup-lightdash/connect-project/#personal-access-token) to get one) 
- repository: lightdash/jaffle_shop
- branch: main
- project directory path: /

### warehouse connection:

First follow [these instructions](./docker/README.md) to run a postgres database for this jaffle shop project.

- type: postgres
- host: host.docker.internal
- user: postgres
- password: password
- database: postgres
- schema: jaffle
- port: 5432
- ssl: disable

## Testing dbt project: `jaffle_shop`

`jaffle_shop` is a fictional ecommerce store. This dbt project transforms raw data from an app database into a customers and orders model ready for analytics.

### What is this repo?
What this repo _is_:
- A self-contained playground dbt project, useful for testing out scripts, and communicating some of the core dbt concepts.

What this repo _is not_:
- A tutorial — check out the [Getting Started Tutorial](https://docs.getdbt.com/tutorial/setting-up) for that. Notably, this repo contains some anti-patterns to make it self-contained, namely the use of seeds instead of sources.
- A demonstration of best practices — check out the [dbt Learn Demo](https://github.com/fishtown-analytics/dbt-learn-demo-v2-archive) repo instead. We want to keep this project as simple as possible. As such, we chose not to implement:
    - our standard file naming patterns (which make more sense on larger projects, rather than this five-model project)
    - a pull request flow
    - CI/CD integrations
- A demonstration of using dbt for a high-complex project, or a demo of advanced features (e.g. macros, packages, hooks, operations) — we're just trying to keep things simple here!

### What's in this repo?
This repo contains [seeds](https://docs.getdbt.com/docs/building-a-dbt-project/seeds) that includes some (fake) raw data from a fictional app.

The raw data consists of customers, orders, and payments, with the following entity-relationship diagram:

![Jaffle Shop ERD](/etc/jaffle_shop_erd.png)


### Running this project
To get up and running with this project:
1. Install dbt using [these instructions](https://docs.getdbt.com/docs/installation).

2. Clone this repository.

3. Change into the `jaffle_shop` directory from the command line:
```bash
$ cd jaffle_shop
```

4. Set up a profile called `jaffle_shop` to connect to a data warehouse by following [these instructions](https://docs.getdbt.com/docs/configure-your-profile). If you have access to a data warehouse, you can use those credentials – we recommend setting your [target schema](https://docs.getdbt.com/docs/configure-your-profile#section-populating-your-profile) to be a new schema (dbt will create the schema for you, as long as you have the right privileges). If you don't have access to an existing data warehouse, you can also setup a local postgres database and connect to it in your profile.

5. Ensure your profile is setup correctly from the command line:
```bash
$ dbt debug
```

6. Load the CSVs with the demo data set. This materializes the CSVs as tables in your target schema. Note that a typical dbt project **does not require this step** since dbt assumes your raw data is already in your warehouse.
```bash
$ dbt seed
```

7. Run the models:
```bash
$ dbt run
```

> **NOTE:** If this steps fails, it might mean that you need to make small changes to the SQL in the models folder to adjust for the flavor of SQL of your target database. Definitely consider this if you are using a community-contributed adapter.

8. Test the output of the models:
```bash
$ dbt test
```

9. Generate documentation for the project:
```bash
$ dbt docs generate
```

10. View the documentation for the project:
```bash
$ dbt docs serve
```

### What is a jaffle?
A jaffle is a toasted sandwich with crimped, sealed edges. Invented in Bondi in 1949, the humble jaffle is an Australian classic. The sealed edges allow jaffle-eaters to enjoy liquid fillings inside the sandwich, which reach temperatures close to the core of the earth during cooking. Often consumed at home after a night out, the most classic filling is tinned spaghetti, while my personal favourite is leftover beef stew with melted cheese.

---
For more information on dbt:
- Read the [introduction to dbt](https://docs.getdbt.com/docs/introduction).
- Read the [dbt viewpoint](https://docs.getdbt.com/docs/about/viewpoint).
- Join the [dbt community](http://community.getdbt.com/).
---
