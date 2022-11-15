# Code Review Example 1

```typescript
export async function updateOrdersOnUpdatedBooking(
  payload: ServiceEvent<FacilityBookingUpdated>
) {
  const repository = new OrderRepository()
  const { prevBooking, newBooking } = payload.data

  if (prevBooking.booking_status !== newBooking.booking_status) {
    for (const orderId of newBooking.transportation_orders) {
      const result = await repository.update(orderId, { status: newBooking.booking_status })
      if (result instanceof Error) {
        LOGGER.error(result)
      }
    }
  }
  if (
    prevBooking.state === FacilityBookingState.ACTIVE &&
    newBooking.state === FacilityBookingState.INACTIVE
  ) {
    for (const orderId of newBooking.transportation_orders) {
      const result = await repository.update(orderId, { facility_booking: null })
      if (result instanceof Error) {
        LOGGER.error(result)
      }
    }
  }

  const ordersRemoved = prevBooking.transportation_orders.filter(
    (order) => !newBooking.transportation_orders.includes(order)
  )
  if (ordersRemoved.length >= 1) {
    for (const orderId of ordersRemoved) {
      const result = await repository.update(orderId, { facility_booking: null })
      if (result instanceof Error) {
        LOGGER.error(result)
      }
    }
  }

  const ordersAdded = newBooking.transportation_orders.filter(
    (order) => !prevBooking.transportation_orders.includes(order)
  )
  if (ordersAdded.length >= 1) {
    for (const orderId of ordersAdded) {
      const result = await repository.update(orderId, { facility_booking: newBooking._id })
      if (result instanceof Error) {
        LOGGER.error(result)
      }
    }
  }
}
```
