clickhouse 案例
##################################################################################

需求描述: 查找 api 峰值及峰值时刻的 code 数据量和成功量。

.. code-block:: python

	import time

	import clickhouse_connect

	client = clickhouse_connect.get_client(host='xxx.xxx.xxx.xxx', port='xxx', username='xxx', password='xxx',
	                                       database='xxxxxx')

	apis = 'aaa,bbb,ccc'.split(',')
	# apis = 'getShopOpenCardInfo'.split(',')

	# api_name = '' # api 名称
	# api_call_count = 0 # 峰值调用量
	# api_timestamp = 0 # 峰值时刻时间戳
	# api_time = '' # 峰值时刻

	for api in apis:
	    # 查询峰值相关信息
	    api_top_info_result = client.command(
	        """select '""" + api + """' as api,sum(call_count) as call_count,timestamp,toString(toDateTime(min(timestamp)), 'Asia/Shanghai') as time from gateway.safe_gateway_risk_monitor_dist where function_id = '""" + api + """' and timestamp >= 1667214000 and timestamp <= 1667998800 group by timestamp as temp order by call_count desc, timestamp asc limit 1;
	    """)

	    api = api_top_info_result[0]
	    api_top_timestamp = api_top_info_result[2]

	    # 查询 code 相关信息
	    api_code_info_result = client.query(
	        """select code, sum(call_count) as sum_count from gateway.gateway_except_stat_dist where function_id = '""" + api + """' and timestamp = """ + api_top_timestamp + """ and (code = '-7' or code = '-9' or code = '-12') group by code""")

	    num_code_7 = 0
	    num_code_9 = 0
	    num_code_12 = 0
	    for api_code_info in api_code_info_result.result_set:
	        if api_code_info[0] == '-7':
	            num_code_7 = api_code_info[1]
	        if api_code_info[0] == '-9':
	            num_code_9 = api_code_info[1]
	        if api_code_info[0] == '-12':
	            num_code_12 = api_code_info[1]

	    api_top_info_result.append(num_code_7)
	    api_top_info_result.append(num_code_9)
	    api_top_info_result.append(num_code_12)

	    # 查询流量出口信息
	    api_flow_monitor_info_result = client.query(
	        """select sum(out_count) from gateway.gateway_flow_monitor_dist where function_id = '""" + api + """' and timestamp = """ +  api_top_timestamp)

	    api_out_count = 0
	    for api_flow_monitor_info in api_flow_monitor_info_result.result_set:
	        api_out_count = api_flow_monitor_info[0]

	    api_top_info_result.append(api_out_count)
	    print(api_top_info_result)

	    time.sleep(1)






























































































































