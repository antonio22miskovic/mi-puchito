<template>
	<div style="height: 100%;" class="container">
		<div class="card">
			<div class="card-body">
				<h1 class="text-center">Actualizar precios:</h1>

				<hr>

				<div class="form-row">
					<div class="col">
						<select name="piso_venta" id="piso_venta" class="form-control" v-model="piso_venta_id" @change="buscar_producto(piso_venta_id)">
							<option value="">Seleccione el piso de venta</option>
							<option :value="piso.id" v-for="(piso, index) in piso_ventas" :key="index">{{piso.nombre}}</option>
						</select>
					</div>
					<!--
					<div class="col">
						<select class="form-control" v-model="metodo" >
							<option value="">Forma de establecer precio</option>
							<option value="1">anclar a un producto</option>
							<option value="2">Actualizar precio manualmente</option>
						</select>
						
					</div>
					-->
					<!--
					<div class="col">
						
						<select name="product" id="product" class="selectpicker border form-control" data-live-search="true" data-width="100%" :disabled="desactivado">
							<option value="">Selecciona un producto</option>
							<option :value="producto.id" v-for="(producto, index) in productos" :key="index">{{producto.name}}</option>
						</select>
					</div>
						{{desactivado}}
					-->
				</div>

				<hr>

				<table class="table table-bordered table-hover table-sm table-stridped">
					<thead>
						<tr>
							<th rowspan="">Producto</th>
							<th>Precio</th>
							<th>Acciones</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(producto, index) in productos" :key="index">
							<td>{{producto.name}}</td>
							<td>{{producto.precio.total_menor}}</td>
							<td>
								<button class="btn btn-danger" @click="showModalPrecio(producto.id)">Actualizar</button>
							</td>


							<!-- Modal -->
							<div class="modal fade" :id="'modal-precios-'+producto.id" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
							  	<div class="modal-dialog modal-lg">
							    	<div class="modal-content">
							      		<div class="modal-header">
							        		<h5 class="modal-title" id="exampleModalLabel">Actualizar precio</h5>
							        		<button type="button" class="close" data-dismiss="modal" aria-label="Close">
							          		<span aria-hidden="true">&times;</span>
							        		</button>
							      		</div>
							      		<div class="modal-body">
							        		
							      			<div class="row">
												<div class="col-md-4">
													<div class="form-group">
														<label for="costo">Costo:</label>
													    <input type="number" class="form-control" id="costo" placeholder="Costo" v-model.number="articulo.costo">
													</div>
												</div>

												<div class="col-md-4">
													<div class="form-group">
														<label for="margen-ganancia">margen de ganancia(%):</label>
													    <input type="number" class="form-control" id="margen-ganancia" placeholder="margen de ganancia" v-model.number="articulo.margen_ganancia">
													</div>
												</div>

												<div class="col-md-4">
													<div class="form-group">
														<label for="iva-porc">Iva(%):</label>
													    <input type="number" class="form-control" id="iva-porc" placeholder="iva" v-model.number="articulo.iva_porc">
													</div>
												</div>

												<div class="col-md-4">
													<div class="form-group">
														<label for="subtotal-menor">Sub total:</label>
													    <input type="number" class="form-control" id="subtotal-menor" placeholder="300000" v-model.number="articulo.sub_total" disabled>
													    <span class="" v-show="false">{{sub_total_comprar}}</span>
													</div>
												</div>

												<div class="col-md-4">
													<div class="form-group">
														<label for="iva">Iva:</label>
													    <input type="number" class="form-control" id="iva" placeholder="50000" v-model.number="articulo.iva" disabled>
													    <span class="" v-show="false">{{iva_total_comprar}}</span>
													</div>
												</div>

												<div class="col-md-4">
													<div class="form-group">
														<label for="total">total:</label>
													    <input type="number" class="form-control" id="total" placeholder="350000" v-model.number="articulo.total" disabled>
													    <span class="" v-show="false">{{total_comprar}}</span>
													</div>
												</div>

											</div>

							     		</div>
							      		<div class="modal-footer">
							        		<button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
							        		<button type="button" class="btn btn-danger" @click="actulizarPrecio(index)">Guardar</button>
							      		</div>
							    	</div>
							  	</div>
							</div>


						</tr>
						<tr v-if="productos == []">
							<td class="text-center">No hay productos registrados</td>
						</tr>
					</tbody>
				</table>
			</div>
			
		</div>
	</div>
</template>

<script>
	export default{
		data(){
			return{
				piso_ventas: [],
				piso_venta_id: "",
				productos: [],
				metodo: "",
				articulo: {
					id: null,
					costo: null,
					iva_porc: null,
					margen_ganancia: null,
					sub_total: null,
					iva: null,
					total: null,
					sub_total_unitario: null,
					iva_unitario: null,
					total_unitario: null,
		        }
			}
		},
		methods:{
			get_piso_ventas(){

				axios.get('/api/get-piso-ventas').then(response => {

					this.piso_ventas = response.data;
					console.log(this.piso_ventas);
				}).catch(e => {
					console.log(e.response)
				});
			},
			buscar_producto(id){

				axios.get('/api/productos/'+id).then(response => {

					console.log(response);
					this.productos = response.data;
				}).catch(e => {
					console.log(e.response)
				});
			},
			showModalPrecio(id){

				let product = this.productos.find(element => element.id == id);
				this.articulo.id = id;
				this.articulo.costo = product.precio.costo;
				this.articulo.iva_porc = product.precio.iva_porc;
				let valor = product.precio.sub_total_menor - product.precio.costo;
				let porc = valor * 100 / product.precio.costo;

				this.articulo.margen_ganancia = porc;

				$('#modal-precios-'+id).modal('show');
			},
			actulizarPrecio(index){

				axios.put('/api/actualizar-precio/'+this.articulo.id, this.articulo).then(response => {

					console.log(response);
					this.productos.splice(index,1, response.data);
				}).catch(e => {
					console.log(e.response)
				});

				$('#modal-precios-'+this.articulo.id).modal('hide');
			}
		},
		computed:{
			desactivado(){
				if (this.metodo != "" && this.piso_venta_id != "") {

					return true;	

				}else{
					return false;
				}
			},
			sub_total_comprar(){
				//SUB TOTAL
				let precioSinIva = ((this.articulo.margen_ganancia * this.articulo.costo) / 100) + this.articulo.costo
				this.articulo.sub_total = precioSinIva

				return this.articulo.sub_total;
			},
			iva_total_comprar(){
				let precioSinIva = ((this.articulo.margen_ganancia * this.articulo.costo) / 100) + this.articulo.costo
				//IVA
				let iva = (this.articulo.iva_porc * precioSinIva) / 100
				this.articulo.iva = iva
				return this.articulo.iva;
			},
			total_comprar(){
				let precioSinIva = ((this.articulo.margen_ganancia * this.articulo.costo) / 100) + this.articulo.costo
				let iva = (this.articulo.iva_porc * precioSinIva) / 100
				//TOTAL
				let total = iva + precioSinIva
				this.articulo.total = total
				return this.articulo.total;
			}

		},
		created(){
			this.get_piso_ventas();
		}
	}
</script>