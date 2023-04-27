
/*
This is a sample function. Uncomment to execute or make changes to this function.
organizationID = organization.get("organization_id");
*/
organizationID = organization.get("organization_id");
// organizationID = "60016864196";
// *List currency api* //
response = invokeurl
[
	url :"https://www.zohoapis.in/books/v3/settings/currencies?organization_id=" + organizationID
	type :GET
	connection:"exchange_rate"
];
// info "responce" + response;
currencies = response.getJSON("currencies");
// info "currencies"+currencies;
for each  currency in currencies
{
	// 	info currency;
	base_currencies = currency.getJSON("is_base_currency");
	// 	info base_currencies;
	if(base_currencies != false)
	{
		Base_currency_code = currency.getJSON("currency_code");
	}
}
// info "Base_currency_code " + Base_currency_code;
// *List currency api* //
// *Exchange rate api* //
exchange = invokeurl
[
	url :"https://openexchangerates.org/api/latest.json?app_id=0ee7a8a4cced47a8b15cb49697f63e66&base=" + Base_currency_code
	type :GET
];
// 	base = exchange.getJSON("base");
exchange_rate = exchange.getJSON("rates");
// *Exchange rate api* //
currentDate = zoho.currentdate.toString("yyyy-MM-dd");
info currentDate;
for each  base in currencies
{
	base_currencies = base.getJSON("is_base_currency");
	if(base_currencies == false)
	{
		currency_id = base.getJSON("currency_id");
		info currency_id;
		currency_code_ex = base.getJSON("currency_code");
		// 		info "currency_code "+currency_code_ex;
		currency_code_ex_value = exchange_rate.getJSON(currency_code_ex);
		info currency_code_ex + currency_code_ex_value;
		if(currency_code_ex_value != null)
		{
			parameters_data = {"rate":currency_code_ex_value,"effective_date":currentDate};
			response = invokeurl
			[
				url :"https://www.zohoapis.in/books/v3/settings/currencies/" + currency_id + "/exchangerates?organization_id=" + organizationID
				type :POST
				parameters:parameters_data.toString()
				connection:"exchange_rate"
			];
			info response;
		}
		else
		{
			sendmail
			[
				from :zoho.adminuserid
				to :zoho.loginuserid
				subject :"The creation of Exchange rate was failed"
				message :"Sorry,The opted currency was not listed"
			]
			info "Sendmail";
		}
	}
}
