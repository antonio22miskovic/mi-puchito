<template>
	<div style="height: 100%;" class="container">
		<div class="alert alert-success" role="alert" v-if="exito == true">
		Enlaze exitoso
		</div>

		<div class="card">
			<div class="card-body">
				<h1 class="text-center">Anclar producto: </h1>

				<hr>

				<div class="form-row">
					<div class="col">
						<select name="piso_venta" id="piso_venta" class="form-control" v-model="piso_venta_id" @change="buscar_producto(piso_venta_id)">
							<option value="">Seleccione el piso de venta</option>
							<option :value="piso.id" v-for="(piso, index) in piso_ventas" :key="index">{{piso.nombre}}</option>
						</select>
					</div>
				</div>

				<br>

				<div class="form-row">
					<div class="col">
						<select name="" id="" class="form-control" v-model="producto">
							<option value="">Seleccione el producto</option>
							<option :value="producto.id" v-for="(producto,index) in productos" :key="index">{{producto.name}}</option>
						</select>
					</div>
					<div class="col">
						<select name="" id="" class="form-control" v-model="producto_anclar">
							<option value="">Seleccione el producto a anclar</option>
							<option :value="producto.id" v-for="(producto,index) in productos_inventory" :key="index">{{producto.product_name}}</option>
						</select>
					</div>
				</div>

				<hr>

				<div class="row">
					<div class="col text-right">
						<button class="btn btn-primary text-right" type="button" @click="enlazar">Enlazar</button>
					</div>
				</div>
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
				productos_inventory: [],
				producto: "",
				producto_anclar: "",
				exito: false
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

				axios.get('/api/requisitos-anclar/'+id).then(response => {

					console.log(response);
					this.productos = response.data.inventario;
					this.productos_inventory = response.data.inventory;
				}).catch(e => {
					console.log(e.response)
				});
			},
			enlazar(){
				this.exito = false;
				let data = {producto_anclar: this.producto_anclar, piso_venta_id: this.piso_venta_id}

				axios.put('/api/enlazar/'+this.producto, data).then(response => {

					console.log(response);
					this.piso_venta_id = "";
					this.productos = "";
					this.productos_inventory = "";
					this.exito = true;
				}).catch(e => {
					console.log(e.response)
				});
			}
		},
		created(){
			this.get_piso_ventas();
		}
	}
</script>