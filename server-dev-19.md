server-dev-19

Failed to generate products: 'NoneType' object has no attribute 'id'  
  
RPC_ERROR

Odoo Server Error

Occured on [odoodemo.snckapp.com](http://odoodemo.snckapp.com) on model purchase.order.line on 2026-08-07 14:16:27 GMT

Traceback (most recent call last):

  File "/opt/odoodemo/odoo/odoo/[http.py](http://http.py)", line 2329, in *serve*db

    return service_model.retrying(serve_func, env=self.env)

           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/odoo/service/[model.py](http://model.py)", line 188, in retrying

    result = func()

             ^^^^^^

  File "/opt/odoodemo/odoo/odoo/[http.py](http://http.py)", line 2384, in *serve*ir_http

    response = self.dispatcher.dispatch(rule.endpoint, args)

               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/odoo/[http.py](http://http.py)", line 2599, in dispatch

    result = self.request.registry['ir.http']._dispatch(endpoint)

             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/odoo/addons/base/models/ir_[http.py](http://http.py)", line 353, in _dispatch

    result = endpoint(**request.params)

             ^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/odoo/[http.py](http://http.py)", line 838, in route_wrapper

    result = endpoint(self, *args,* *params_ok)

             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/addons/web/controllers/[dataset.py](http://dataset.py)", line 32, in call_kw

    return call_kw(request.env[model], method, args, kwargs)

           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/odoo/service/[model.py](http://model.py)", line 97, in call_kw

    result = method(recs, *args,* *kwargs)

             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/addons/web/models/[models.py](http://models.py)", line 2162, in onchange

    record._apply_onchange_methods(field_name, result, visited_onchanges)

  File "/opt/odoodemo/odoo/odoo/orm/[models.py](http://models.py)", line 6982, in *apply*onchange_methods

    res = method(self)

          ^^^^^^^^^^^^

  File "/opt/odoodemo/odoo/custom_addons/aof_m3/models/purchase_[order.py](http://order.py)", line 190, in *onchange*product_id_m3_dimensions

    self._recalculate_m3_metrics()

  File "/opt/odoodemo/odoo/custom_addons/aof_m3/models/purchase_[order.py](http://order.py)", line 305, in *recalculate*m3_metrics

    elif not line.product_packaging_id and pieces > 0:

             ^^^^^^^^^^^^^^^^^^^^^^^^^

AttributeError: 'purchase.order.line' object has no attribute 'product_packaging_id'

The above server error caused the following client error:

RPC_ERROR: Odoo Server Error

    RPC_ERROR

        at makeErrorFromResponse ([https://odoodemo.snckapp.com/web/assets/21dc858/web.assets_web.min.js:3195:165](https://odoodemo.snckapp.com/web/assets/21dc858/web.assets_web.min.js:3195:165))

        at XMLHttpRequest.<anonymous> ([https://odoodemo.snckapp.com/web/assets/21dc858/web.assets_web.min.js:3202:13](https://odoodemo.snckapp.com/web/assets/21dc858/web.assets_web.min.js:3202:13))  
  
  
**Invalid Operation**

Quant's editing is restricted, you can't do this operation.