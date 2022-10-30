# Deephaven-live-crypto-trading
os windows

1. Make sure that the latest Java JDK installed in your system
2. In command prompt or anakonda prompt type 
   java --version to check if the java jdk was installed. Make sure you have the latest version of java jdk.
3. Copy the path location of java jdk in your system using jshell: 
4. jshell> System.getProperty("java.home"). copy the path
5. exit jshell: /exit
6. create the virtual enviroment: 
   python3 -m venv crypto
7. and activate it:
   conda activate crypto
8. set JAVA_HOME="your path"
9. install deephaven:
    python -m deephaven_example_app
    
    As soon the deephaven was installed you will seee the local server in the prompt
    http://localhost:10000/ide/

10. Live traiding:

#remove columns from live table:
my_table = matches_table.drop_columns("type")

#check specific coins:
BTC=my_table.where("product_id== 'BTC-USD'")
ETH=my_table.where("product_id== 'ETH-USD'")


#specify the amount in total transactions, for example no less than 0.25:
#highlight the transaction with red color:

BTC=my_table.where("product_id== 'BTC-USD'").format_row_where("size>0.25", "DEEP_RED")

#create new table with new data:
large_BTC =my_table.where(["product_id== 'ETH-USD'", "size>0.25"])

#plot the data:
from deephaven.plot.figure import Figure

figure = Figure()

BTC_fig = figure.plot_xy(series_name="BTC-USD", t=BTC.reverse().tail(500), x="time", y="price").show()
ETH_fig = figure.plot_xy(series_name="ETH-USD", t=ETH.reverse().tail(500), x="time", y="price").show()

#plot data in the different time interval:
nanos_minute = 60_000_000_000

BTC_minute = BTC.select(["time", "price"]).update("time=lowerBin(time, nanos_minute)").avg_by("time").rename_columns("BTC_price=price")
ETH_minute = ETH.select(["time", "price"]).update("time=lowerBin(time, nanos_minute)").avg_by("time").rename_columns("ETH_price=price")

#check the ratio of BTC to ETH:
combo=BTC_minute.join(table=ETH_minute, on="time")
combo.update(["ETH_BTC = ETH_price / BTC_price", "BTC_ETH = BTC_price / ETH_price"])
