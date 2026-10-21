オイル温度予測による設備運用・保全支援
概要

本プロジェクトでは、設備センサデータを用いてオイル温度（OT）の将来予測を行った。
EDA（探索的データ分析）の結果、オイル温度には強い時間的連続性が存在すると考えられたため、ラグ特徴量を作成し、RandomForestによる3時間先の温度予測モデルを構築した。
また、予測結果をもとに設備運用・保全への活用可能性について考察した。
データセット

使用データ：
ETTh1

主な変数：
HUFL
HULL
MUFL
MULL
LUFL
LULL
OT（Oil Temperature）
課題設定
目的変数として3時間後のオイル温度を設定した。

Python
df["target"] = df["OT"].shift(-3)

モデルは現在および過去の情報から3時間後の温度を予測する。

実施内容
1. 前処理
日時型への変換
時刻特徴量(hour)の作成
欠損値除去
Python
df["date"] = pd.to_datetime(df["date"])
df["hour"] = df["date"].dt.hour
2. EDA
実施内容

オイル温度の時系列推移確認
特徴量の分布確認
相関関係の確認

得られた示唆

温度変化は比較的滑らか
現在の温度と将来温度に強い関係が存在
ラグ特徴量が有効と考えられる
3. 特徴量エンジニアリング

以下のラグ特徴量を作成した。

Python
OT_lag1
OT_lag3
OT_lag24

使用特徴量

Plain Text
HUFL
HULL
MUFL
MULL
LUFL
LULL
hour
OT_lag1
OT_lag3
OT_lag24
4. モデル構築

使用モデル

Python
RandomForestRegressor

データ分割

Plain Text
Train : 80%
Test : 20%

時系列データであるため、シャッフルは行わず時系列順を維持した。

結果

評価指標

Plain Text
MAE = 0.884
RMSE = 1.226

結果より、

温度変動の傾向を概ね再現できた
平均約0.88℃の誤差で予測可能であった
特徴量重要度
Feature	ImportanceOT_lag1	0.971
hour	0.008
OT_lag24	0.004
MUFL	0.003
OT_lag3	0.003

考察

OT_lag1が全体の約97%を占めた
直近の温度が将来温度予測に強く寄与している
温度には強い自己相関が存在することが確認された
運用価値の考察

本モデルにより、将来の温度上昇を事前に予測できる可能性が示された。

期待される活用例

異常温度の早期検知
設備停止リスクの低減
点検計画の最適化
状態基準保全（Condition Based Maintenance）への活用
課題と今後の改善

課題
OT_lag1への依存が大きい
設備状態量の活用度が低い
3時間先予測のみを対象としている

今後の改善
より長期の予測（6時間後、24時間後）
XGBoost等との性能比較
特徴量エンジニアリングの改善
異常検知タスクへの発展
実行環境
Plain Text
Python 3.x
pandas
numpy
scikit-learn
matplotlib

参考文献、ツール
https://www.kaggle.com/learn/intermediate-machine-learning
https://www.kaggle.com/learn/intro-to-machine-learning
https://copilot.com/ja-jp/
https://gamma.app/
